# Model
How your Rails application communicates with a data store

We can run `t = Tweet.find(3)` because we have a model in our Rails application called `Tweet` which resides in `app/models/tweet.rb' and will look something like
```
class Tweet < ActiveRecord::Base

end
```
This means that the `Tweet` model inherits from `ActiveRecord::Base` which is what maps the `Tweet` class to the `tweets` table in the database.

## Validation
We can validate Rails models to ensure that we only save the correct values into our database.  

The following will validate that `status` is not blank and only allow us to save the record into the database when it passes the validation.
```
class Tweet < ActiveRecord::Base
    validates_presence_of :status
end
```

Use `ModelName.errors.messages` to see the hash of the  error messages for the validation check and `ModelName.errors[:key][0]` to access the String message for the corresponding attribute.

Some out of the box validations include:
- `validates_presence_of :status`
- `validates_numericality_of :fingers`
- `validates_uniqueness_of :toothmarks`
- `validates_confirmation_of :password`
- `validates_acceptance_of :zombification`
- `validates_length_of :password, minimum: 3`
- `validates_format_of :email, with: /regex/i`
- `validates_inclusion_of :age, in 21..99`
- `validates_exclusion_of :age, in 0..21, message: "Sorry, you must be over 21"`

An easier syntax to perform validations is `validates :attribute, <validation>`, for example:
```
validates :status, 
    presence: true, 
    length: { minimum: 3 }

Additional options include
    presence: true
    uniqueness: true
    numericality: true
    length: { minimum: 0, maximum: 2000 }
    format { with /.*/ }
    acceptance: true
    confirmation: true
```

## Relationships
If a `Zombie` can have many `tweets` and each `Tweet` belongs to only one `Zombie`, these can be expressed in the models as follows. Notice that `tweets` is plural while `zombie` is singular in this case.
```
class Zombie < ActiveRecord::Base
    has_many :tweets
end

class Tweet < ActiveRecord::Base
    belongs_to :zombie
end
```