- Migrations are a convenient way to alter your database schema over time in a consistent way.
- Its database type independent
- Can also be use in rollback
- can use `reversible` for complex rollbacks
- to generate migrations : `rails g migrate <migration_name>`

## Migrations to create a table

```rb
class CreateArticles < ActiveRecord::Migration[7.0]
  def change
    create_table :articles do |t|
      t.string :title
      t.text :body
      t.timestamps
    end
  end
end
```

## Generate migrations through rails

Rails can genrate basic migrations through terminal

- `bin/rails generate migration AddPartNumberToProducts part_number:string
`

```rb
class AddPartNumberToProducts < ActiveRecord::Migration[7.0]
  def change
    add_column :products, :part_number, :string
  end
end
```

---

- `bin/rails generate migration RemovePartNumberFromProducts part_number:string
`

```rb
class RemovePartNumberFromProducts < ActiveRecord::Migration[7.0]
  def change
    remove_column :products, :part_number, :string
  end
end
```

---

- To generate more than one columns use `AddDetailsToTable` <br/><br/>
  `bin/rails generate migration AddDetailsToProducts part_number:string price:decimal
`

```rb
class AddDetailsToProducts < ActiveRecord::Migration[7.0]
  def change
    add_column :products, :part_number, :string
    add_column :products, :price, :decimal
  end
end
```

---

- To create table we use `CreateXXX` along with column name and types it will cerate a Table out of it <br>
  `bin/rails generate migration CreateProducts name:string part_number:string`

## Model generation

- When we create a model a migrate automatically generated for us<br>
  `bin/rails generate model Product name:string description:text
`

```rb
class CreateProducts < ActiveRecord::Migration[7.0]
  def change
    create_table :products do |t|
      t.string :name
      t.text :description

      t.timestamps
    end
  end
end
```

## Writing Own Migrations

### Creating a table

```rb
create_table :products do |t|
  t.string :name, null: false
  t.integer :age
  t.text :body
  t.decimal :price
end
```

### Creating Join Table

```rb
create_join_table :products, :categories
```

which creates a categories_products table with two columns called category_id and product_id. These columns have the option :null set to false by default. This can be overridden by specifying the :column_options option:

### Changing Table

```rb
change_table :products do |t|
  t.remove :description, :name
  t.string :part_number
  t.index :part_number
  t.rename :upccode, :upc_code
end
```

### Changing Types of Columns

```rb
change_column :products, :part_number, :text
```

## To create custom rollback `up/down`

- Simple actions like Adding an column can be easily rolledback without any extra code
- But in some migrations we need to add `reversible` for custom rollback

```rb
class ChangeTypeInDemos < ActiveRecord::Migration[7.0]
  def change
    reversible do |dir|
      # When we update the migration
      dir.up do
        change_column :demos, :desc, :text
      end

      # Down when we rollback the migration
      dir.down do |dir|
        change_column :demos, :desc, :string
      end
    end
  end
end
```

Run a specific migration <br>
`rails db:migrate VERSION=20080906120000`

Rollback last migrations <br>
`rails db:rollback VERSION=2000329393284`

Rollback 'n' steps <br>
`rails db:migrate STEP=3`
