- Validations are used to ensure that only valid data is saved into your database.
- Model Level Validations
- Validation Done Before insert and update queries

```
Validations done before these
- create
- create!
- save
- save!
- update
- update!
```

```rb
# TO skip Validations
.save(validate: false)
```

### TO check validity we use `valid? / invalid?`

```rb
user1 = User.new do |u|
    u.name = "Kushal"
    u.email = "kushal@gmail.com"
end

user1.valid? #true
```

### To get errors after validation `.errors` ([[ActivateRecordsErrors]])

The errors can be accessed by `errors`. It should be empty for a validation true.

```rb
user.errors

# It will give the message
user.errors.object.name.full_message

# TO get totoal number of errors
users.errors.size
```

## Acceptance

This method validates that a checkbox on the user interface was checked when a form was submitted. This is typically used when the user needs to agree to your application's terms of service, confirm that some text is read, or any similar concept.

```rb
# only true is allowed
class Person < ApplicationRecord
  validates :terms_of_service, acceptance: true
end
```

```rb
# Custom default error message when not valid
class Person < ApplicationRecord
  validates :terms_of_service, acceptance: { message: 'must be abided' }
end
```

`accept` used to add custom accepted values

```rb
class Person < ApplicationRecord
  validates :terms_of_service, acceptance: { accept: 'yes' }
  validates :eula, acceptance: { accept: ['TRUE', 'accepted'] }
end
```

#### To validate all associated values

```rb
class Library < ApplicationRecord
  has_many :books
  validates_associated :books
end
```

## Confirmation

- When we want two values to be exactly same
- The name of field will be `email` & `email_confirmation`
- It only checks when the fields are not null so we also need to add `presence: true`

```rb
class Person < ApplicationRecord
    validates :email, confirmation: true
    validates :email_confirmation, presence: true
end
```

## Comparisons

To add comparisons

```rb
class Promotion < ApplicationRecord
  validates :start_date, comparison: { greater_than: :end_date }
end
```

## Inclusion

To check if field value is in the given set

```rb
class Coffee < ApplicationRecord
  validates :size, inclusion: { in: %w(small medium large),
    message: "%{value} is not a valid size" }
end
```

## Length

For validation of min and max length

```rb
class Person < ApplicationRecord
  validates :name, length: { minimum: 2 }
  validates :bio, length: { maximum: 500 }
  validates :password, length: { in: 6..20 }
  validates :registration_number, length: { is: 6 }
end
```

## And Many More

- presence
- absence
- uniqueness
- format
- exclusion

---

## Custom Validators

When default validations are not enough and we want to add custom logic for validations

```rb
class GoodnessValidator < ActiveModel::Validator
  def validate(record)
    if options[:fields].any? { |field| record.send(field) == "Evil" }
      record.errors.add :base, "This person is evil"
    end
  end
end

class Person < ApplicationRecord
  validates_with GoodnessValidator, fields: [:first_name, :last_name]
end

```
