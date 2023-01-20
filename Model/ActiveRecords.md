Active Record facilitates the creation and use of business objects whose data requires persistent storage to a database.

---

### Object Relational Mapping(ORM)

Object Relational Mapping, commonly referred to as its abbreviation ORM, is a technique that connects the rich objects of an application to tables in a relational database management system.

#### To Create Active Records

`rails generate model user name:string occupation: string`

```rb
class User < ApplicationRecord
end
```

## CRUD Operations

### Create :

**Create and save**

```rb
# It will create an user and also save in database
user = User.create(name: "David", occupation: "Code Artist")
```

**Instantiated**

```rb
# First Way
user = User.new
user.name = "David"
user.occupation = "Code Artist"

# Second Way
user = User.new do|u|
    u.name = "David"
    u.occupation = "Code Artist"
end
```

---

### Read :

```rb
# return a collection with all users
users = User.all

# return the first user
user = User.first

# return the first user named David
david = User.find_by(name: 'David')


# find all users named David who are Code Artists and sort by created_at in reverse chronological order
users = User.where(name: 'David', occupation: 'Code Artist').order(created_at: :desc)
```

---

### Update :

```rb
# Using Save
user = User.find_by(name: 'David')
user.name = 'Dave'
user.save
```

```rb
# Using update
user = User.find_by(name: 'David')
user.update(name: 'Dave')
```

```rb
User.update(:all, max_login_attempts: 3, must_change_password: true)
```

---

### Delete :

```rb
user = User.find_by(name: 'David')
user.destroy
```

# Validations([[ActiveRecordsValidations]])

```rb
class User < ApplicationRecord
  validates :name, presence: true
end
```
