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

Tweet.create(hash)
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

Tweet.create(status: "I <3 brains", zombie: "Jim")
```
### Read (Accessing tables)
A `singular` and `uppercase` table name in rails will allow you to access the `plural` and `lowercase` table name in the database.

- `Tweet.find(2)` returns a single tweet
- `Tweet.find(3, 4, 5)` returns an array of tweets
- `Tweet.find(first)` returns the first tweet
- `Tweet.find(last)` returns the last tweet
- `Tweet.find(all)` returns all the tweets
- `Tweet.count` returns total number of tweets
- `Tweet.order(:zombie)` returns all tweets, ordered by zombies
- `Tweet.limit(10)` returns first 10 tweets
- `Tweet.where(zombie: "ash")` returns all tweets from a zombie named "ash"

#### Method Chaining
`Tweet.where(zombie: "ash").order(:status).limit(10)` will return the first 10 tweets only from a zombie named 'ash' 

Example:
- `t = Tweet.find(3)` will search the `tweets` table for the tweet with id = 3.
- `puts t[:id]` will return 3.
- Alternate syntax will be the `dot` syntax such as `t.id`.

### Update
Recipe
```
t = TableName.find(id)
t.key = value
t.save
```
Alternative using Hashes
```
t = TableName.find(id)
t.attributes = hash
t.save

or

t = TableName.find(id)
t = TableName.update(Hash)
```

- Fetch the data `t = Tweet.find(3)`.
- Update the value `t.zombie = "EyeballChomper".
- Save the updates `t.save`.

Using Hashes
```
t = Tweet.find(2)
t.attributes = {
    status: "Can I munch your eyeballs?",
    zombie: "EyeballChomper"}
t.save

or 

t = Tweet.find(2)
t.update(
    status: "Can I munch your eyeballs?",
    zombie: "EyeballChomper")
```
### Delete
Recipe
```
t = Table.find(id)
t.destroy

or 

TableName.find(id).destroy
```
To delete everything from a table use `TableName.destroy_all`