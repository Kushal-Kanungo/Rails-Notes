These are also called as **Filters**
This **filter** will before every controller action

```rb
class ApplicationController < ActionController::Base

	before_action :print_display_message, only %i[show edit destroy] 
	after_action :print_after_display_message
	private

	def print_display_message
		puts "I am inherited before action filter "
	end

	def print_after_display_message
		puts "I am inherited after action filter "
	end
end
```

