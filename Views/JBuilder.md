It is another way to render views in rails.

### Steps to follow
1. Add `gem "jbuilder"` in **Gemfile**.
2. Now in the views folder create file according to controller
*For eg*
	- Our cotroller name is `user_controller.rb` and action name is `login`
	- So create a folder in `views` folder named `users` and create a file named `login.json.jbuilder`
```rb
json.data do
  json.user do
    json.call(
      @user,
      :id,
      :username
    )
  end
  json.token @token
end
```

> Note: We cannot access local variables in views so we need to add `@` in variables we want to use in our jbuilder views.

