# Ruby on Rails

## CRUD
### Create
Recipe
```
t = TableName.new
t.key = value
t.save
````
Alternatively, you can also use a Hash. Using the create method automatically saves it into the database.
```
t = Tweet.new(hash)
t.save

or 

Tweet.create(status: "I <3 brains", zombie: "Jim")
```
Example
```
t = Tweet.new
t.status = "I <3 brains."
t.save
```
Using a Hash 
```
t = Tweet.new(
    status: "I <3 brains",
    zombie: "Jim")
t.save

or 

TableName.create(hash)
```
### Read (Accessing tables)
A `singular` and `uppercase` table name in rails will allow you to access the `plural` and `lowercase` table name in the database.

Example:
- `t = Tweet.find(3)` will search the `tweets` table for the tweet with id = 3.
- `puts t[:id]` will return 3.
- Alternate syntax will be the `dot` syntax such as `t.id`.

### Update
- Fetch the data `t = Tweet.find(3)`.
- Update the value `t.zombie = "EyeballChomper".
- Save the updates `t.save`.

### Delete
`t.destroy`