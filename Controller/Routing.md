
> The Rails router recognizes URLs and dispatches them to a controller's action
> Routes are matchded in the sequence they specified

### Connecting URL to code
- `get '/patients/:id', to: 'patients#show'`
- `get '/about' => 'welcome#about'`
- Rails uses snake_case for controller names here, if you have a multiple word controller like `MonsterTrucksController`, you want to use `monster_trucks#show` for example.

---

### Resource Route
It can quickly create common routes of a common operations
- Get
- Put
- Post
- Destroy
- Get by index

### Member & Collection Routing

1. **Member Routing** : Routing which are performed on single instance by using id. For rg. `get '/students/:id'`
2. **Collection Routing** : Routing which include collection of our model. For eg: `get '/students'`

#### Member Routing

```rb
resources :students do
	member do
		get :personal_details
	end
end
```

Route Created by upper code : `/students/:id/personal_details`

---

#### Collection Routing

```rb
resources :students do
	collection do
		get :active
	end
end
```

Route Created by upper code : `/students/active`

---

### Namespace and Scoped Routing

#### Namespace Routing 
When we want to organize group of routings under a namespace.

*For eg.* We want to sepearate routing for admin purposes.

1. First we create a directory under the **controller folder**
2. So directory will be like this : `controller/admin`
3. In routes file we will write
 ```rb
 # It will find student route in admin file
 namespace :admin do
	 resources :students
 end 
```
4. Create a file `students_controller.rb` in `admin` folder in `controllers` folder.
```rb
modeule Admin
	class StudentsController < ApplicationController
		# Controllers Functions
	end
end
```
5. Now we can implement **CRUD** operations in this file.
6. Routes will be like this  `get '/admin/students'`

---

#### Scope Routing
It will also create use to group routing but it do not add prefix like **admin** in the routes unlike namespace routing.

```rb
scope module: :admin do
	resources :students
end
```
So the routes will be like this : `get '/students'`
