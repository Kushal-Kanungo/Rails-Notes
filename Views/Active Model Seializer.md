
## Steps to Follow

1. Add `gem "active_model_serializer"` in the **Gemfile**
2. Generate a serializer for the model in user case it is `user`
	```bash
	rails g serializer user
```

3. It will create a `user_serializer.rb` file.
4. In this file add the attributes we want to send in json response of @user.
```rb
class UserSerializer < ActiveModel::Serializer
  attributes :id, :username, :token

  # Because token is not part of the model we extracted it from the instance options
  # the instance options are the attributes we added with @user in `render json:`
  def token
    @instance_options[:token]
  end
end
```
