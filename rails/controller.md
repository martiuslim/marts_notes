# Controller
`Brains` of the application that control the [Models](/model.md) & [Views](/view.md)

When a `request` such as `/tweets/1` comes in, it will first be handled by a `controller` such as `/app/controllers/tweets_controller.rb` before rendering the `view`. 

The file directory is seen below:
```
zombie_twitter
    |- app
        |- controllers
            |- tweets_controllers.rb
```
Example of a `Controller`: 
```
class TweetsController < ApplicationController
  def show
    @tweet = Tweet.find(params[:id])
    render action: 'status'
  end
end
```
- The `show` method is typically where the calls to the models are found. 
- Using the `@` symbol scopes the variables into `instance variables` and grants the `view` access to these variables.
- params recipe: `params = { id : "1" }`

## Parameters
Rails will take a URL like `/tweets?status=I'm dead` to generate `params = { status : "I'm dead" }`.  
To access a value from a Hash within a Hash such as from `/tweets?tweet[status]=I'm dead`,  we can use the simpler and alternate syntax `@tweet = Tweet.create(params[:tweet])`.  

### Strong Parameters
Rails requires us to specify the parameter `key` we require as well as the `attributes` we permit to be set **only** when `creating` or `updating` with `multiple attributes`.    
As such we have to rewrite the code above to become `@tweet = Tweet.create(params require(:tweet) permit(:status))`. For multiple attributes to be permitted, we can use an array such that `params.require(:tweet).permit([:status, :location])`.

## Responding with XML/ JSON
We can send in a request such as `/tweets/1.json` or `/tweets/1.xml` to get a `json` or `xml` response. We can then modify our controller to be as such:
```
class TweetsController < ApplicationController
  def show
    @tweet = Tweet.find(params[:id])
    respond_to do |format|
      format.html
      format.json { render json: @tweet }
      format.xml { render xml: @tweet }
  end
end
```

## Controller Actions
```
class TweetsController < ApplicationController
  def index     # List all tweets
  def show      # Show a single tweet
  def new       # Show a new tweet form
  def edit      # Show an edit tweet form
  def create    # Create a new tweet
  def update    # Update a tweet
  def destroy   # Delete a tweet
```

## Authentication 
Use `session` (works like a per user hash) to check if user is allowed to `edit` or `destroy` the tweets specified. Here, `flash` is used to show an error message to the user and he is redirected away from the edit page if he is not allowed to perform the edit.
```
def edit
  @tweet = Tweet.find(params[:id])
  if session[:zombie_id] != @tweet.zombie_id
    flash[:notice] = "Sorry, you can't edit this tweet"
    redirect_to(tweets_path)
end
```

## Notice for Layouts
Earlier we used `flash` to show a message to the user, but how do we specify where the message is shown? We do this in `layouts` under `/app/views/layouts/application.html.erb`.
```
<!DOCTYPE HTML>
  <head>
    <title>RailsForZombies</title>
  </head>
  <body>
    <header>...</header>
    
    <% if flash[:notice] %>
      <div id="notice"><%= flash[:notice] %></div>
    <% end %>
    <%= yield %>
  </body>
</html>
```

## D.R.Y. (Don't Repeat Yourself)
Since `edit`, `update` and `destroy` all require authorization, we can abstract out the code as such:

```
before_action :get_tweet, only: [:edit, :update,  :destroy]
before_action :check_auth, :only => [:edit, :update, :destroy]

def get_tweet
  @tweet = Tweet.find(params[:id])
end

def check_auth
  if session[:zombie_id] != @tweet.zombie_id
    flash[:notice] = "Sorry, you can't edit this tweet"
    redirect_to(tweet_path)
  end
end
```
