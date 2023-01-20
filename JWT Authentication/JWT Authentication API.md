### Authentication

1. Add `gem "bcrypt", "~> 3.1.7"` in Gemfile. It will use to hash the password.

2. Create a User model
	- `rails g model user username:string password_digest:string --no-test-framework`
	- `password_digest` : will create two columns in table *password* & *password_confirmation*

3. In the user model add `has_secure_password` method
```rb
class User < ApplicationRecord
	# It will automatically save our password hashed in the table
	has_secure_password
end
```

4. Run the migration using `rails db:migrate`

5. Check the `schema.rb` file, it should be like this
```rb
ActiveRecord::Schema[7.0].define(version: 2023_01_19_044349) do
  create_table "users", force: :cascade do |t|
    t.string "username"
    t.string "password_digest"
    t.datetime "created_at", null: false
    t.datetime "updated_at", null: false
  end
end
```

6. Add `gem jwt` in **Gemfile**

7. Add `encode_token` method in **ApplicationController**, so it will be available to all controller inheriting it.
```rb
class ApplicationController < ActionController::API
  def encode_token(payload)
    JWT.encode(payload, 'secret')
  end
end
```

8. Create a **user** controller using rails cli: `rails g controller users`.

9. Create a `create` action in **UserController** to *register* an user and return a token.
```rb
class UsersController < ApplicationController
  def create
    @user = User.create(user_params)
    if @user.valid?
	  # It will create a token of that user	
      token = encode_token({ user_id: @user.id })
      render json: { user: @user, token: token }, status: :ok
    else
      render json: { error: 'Invalid UserName or Password' }, status: :unprocessable_entity
    end
  end

  private

  def user_params
    params.require(:user).permit(:email, :password)
  end
end
```

10. Add route for register in `routes.rb` file.
```rb
Rails.application.routes.draw do
  resource :users, only: [:create]
end
```

11. To make a little bit more secure add Validations for password confirmation. And also add `password_confirmation` in permit params.
```rb
class User < ApplicationRecord
  has_secure_password
  validates :username, presence: true, uniqueness: true
  validates :password, confirmation: true
  validates :password_confirmation, presence: true
end
```

12. Now test the registeration method using **Postman**
13. Pass the json body like this
```json
{
    "user": {
        "username" : "Kushal",
        "password" : "123456789",
        "password_confirmation" : "123456789"
    }
}
```

14. Now create a `login` action in **UsersController**
```rb
  def login
    @user = User.find_by(username: user_params_login[:username])

    # authenticate method from bycrpt
    # same as @user && authenticate(user_params_login[:password])
    if @user&.authenticate(user_params_login[:password])
      # Create a token if password is correct
      token = encode_token({ user_id: @user.id })
      render json: { user: @user, token: token }, status: :ok
    else
      render json: { error: 'Username Or Password is incorrect' }, status: :unprocessable_entity
    end
  end

  private
    def user_params_login
	    params.require(:user).permit(:username, :password)
	end
```

15. Also add `login` collection route in `route.rb` file. I've also changed `create` to `register` route.
```rb
Rails.application.routes.draw do
  resource :users, only: [:create] do
    member do
      post :register
      post :login
    end
  end
end
```

### Authorization

16. Add a `decode_token` method in **ApplicationController**
> It will decode the token use the secret key and give us to `user_id` stored in it
```rb
def decode_token
    unless auth_header
      auth_header = request.headers['Authorization']
      return
    end

    # extract the token
    token = auth_header.split(' ')[1]
    begin
      JWT.decode(token, 'secret', true, algorithm: 'HS256')
      # It will return like this : ? [{"user_id"=>"123"}, {"alg"=>"HS256"}]
    rescue JWT::DecodeError
      nil
    end
end
```

17. Create a method `authorized_user` in **ApplicationController** which will find the user in our database and set `@user` to it. *i.e. `@user` is authorised now*
```rb
# This method will find the User Object in the table by using user_id which was in token
  def authorized_user
    decoded_token = decode_token()
    if decode_token
      user_id = decoded_token[0]['user_id']
      @user = User.find(user_id)
    end
  end

  # This method will render the message with unthorized status code
  def authorize
    render json: {message: 'You have to log in.'}, status: :unauthroized unless authorized_user
  end
```

18. Now to add some secure routes first scaffold a **Book Model**
	`rails g scaffold Book title:string user:references --no-test-framework`

19. Add before action in **BooksController** 
> It will make all action in books controller secured and require authorization

```rb
	class BooksController < ApplicationController
		before_action :authorize
	end
```

20. Now just migrate the Book Model to database using `rails db:migrate`
21. To implement **one to many** associations between user and book.
	- Add `has_may :books` in **User Model**
	- Add `belongs_to` in **Book Model**
	