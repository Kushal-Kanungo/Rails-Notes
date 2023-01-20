> Action Controller is **C** in MVC. 
> After the router has determined which controller to use for a request, the controller is responsible for making sense of the request and producing the appropriate output.

--- 

### Naming Convention 
- Pluralization
- For eg. `ArticlesController` , `StudentsController`

### Methods and Actions
> A controller is a Ruby class which inherits from `ApplicationController` and has methods just like any other class. When your application receives a request, the routing will determine which controller and action to run, then Rails creates an instance of that controller and runs the method with the same name as the action.

```rb
class ClientsController < ApplicationController
  def new
  end
end
```

## Parameters
We want access data sent by user, it can be
- **Query String Parameters** : These are the part of *URL*
- **Post Data Parameters**
Rails does not distinct in these two parameters and treat them same
These can be accessed through a hash `params`
- `params[:name]`
- `params[:email]`

#### Arrays and Hash Parameters
It can contain nested arrays and hashes. To send an array of values, append an empty pair of square brackets "[]" to the key name:

`GET /clients?ids[]=1&ids[]=2&ids[]=3`
It will be encoded as this, beacuse `[ ]` aren not allowed in urls
`/clients?ids%5b%5d=1&ids%5b%5d=2&ids%5b%5d=3`

#### JSON Parameters
`{ "company": { "name": "acme", "address": "123 Carrot Street" } }`

We can grab this like this : `params[:company]`


#### Routing Parameters
`get '/clients/:status', to: 'clients#index', foo: 'bar'`

It can be grabbed by `params[:foo]`

#### Strong Parameters
- It is used to improve security
- We first permit which parameters to whitelist when updating by using `.permit`
- We can also use `.require` so that, if all params are not provided by the client, it can raise and error for bad request 400
```rb
class PeopleController < ActionController::Base
  def create
    Person.create(params[:person])
  end

  def update
    person = current_account.people.find(params[:id])
    person.update!(person_params)
    redirect_to person
  end

  private
    def person_params
      params.require(:person).permit(:name, :age)
    end
end

```

##### Permitted Scalar Values

- `params[id: [] ]` -> Now id should be of array type
- `params.permit(preferences: {})` -> prefrences should be a hash map
- We can also use nested permit parameters :
```rb
params.permit(:name, { emails: [] },
              friends: [ :name,
                         { family: [ :name ], hobbies: [] }])

```

> We can use **fetch** if param is not provided
> `params.fetch(:blog, {}).permit(:title, :author)``

```rb
def product_params
  params.require(:product).permit(:name, data: {})
end
```
