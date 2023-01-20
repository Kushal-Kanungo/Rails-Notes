To check an object is valid use `valid? / invalid?`

## errors[]

errors[] is used when you want to check the error messages for a specific attribute. It returns an array of strings with all error messages for the given attribute, each string with one error message. If there are no errors related to the attribute, it returns an empty array.

```rb
person = Person.new(name: "JD")
person.valid?
person.errors[:name] #["is too short (minimum is 3 characters)"]
```
