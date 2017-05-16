# Routing

In order to properly find these paths such as `<%= link_to "<link text>, <code> %>` which link to  the `code` column in

| Action | Code | URL Generated |
| ------ | ---- | ------------- | 
| List all tweets | tweets_path | /tweets |
| New tweet form | new_tweet_path | /tweets/new |
| Show a tweet | tweet | /tweets/1 |
| Edit a tweet | edit_tweet_path(tweet) | /tweets/1/edit |
| Delete a tweet | tweet, :method => :delete | /tweets/1 |

and have them route into these `actions`

```
class TweetsController < ApplicationController
  def index
  def show
  def new
  def edit
  def create
  def update
  def destroy
end
```

We need to properly define `routes` inside our `router` to create a `"REST"ful` resource. This is found in the `routes.rb` file under `config`. The file directory is seen below

```
zombie_twitter
	|- config
    	|- routes.rb
```

`routes.rb` will look something like this

```
ZombieTwitter::Application.routes.draw do
  resources :tweets
end
```
Just `resources :tweets` alone gives us the following URL generators to use

| Code | URL | TweetsController | 
| ---- | --- | ---------------- | 
| tweets_path | /tweets | def index |
| tweet | /tweet/\<id> | def show |
| new_tweet_path | /tweets/new | def new |
| edit_tweet_path(tweet) | /tweets/\<id>/edit | def edit |

## Custom Routes

We can create custom routes that map to the same controllers & actions. For example, we may want `/new_tweet` to map to the same action as `/tweets/new`. 

In this case, we can add a new line in `routes.rb` that maps the `path` to the respective `controller` and `action` such as `get '/new_tweet' => 'tweets#new'`.

## Named Routes

Let's say we want the user to see what is rendered in `/tweets` when he accesses `/all`. In this case, we are accessing the `index` action inside `TweetsController`. As such, we will write `get '/all' => 'tweets#index'` in `routes.rb`.

To be able to use this URL as a link, we can specify the path using `get '/all' => 'tweets#index', as: 'all_tweets'` so that we can use the link path with `<%= link_to 'All Tweets', all_tweets_path %>`.

## Redirect

If we want a definitive URL such as `/tweets` instead of `/all`, we can simply redirect the user using `get '/all' => redirect('/tweets')`.

## Root Route

To specify where the user is routed to when he visits the root domain, use `root to: "tweets#index"`. To link to the root route, use `<%= link_to "All Tweets", root_path %>`

## Route Parameters

Since `TweetController` has an `index` action that lists all the tweets, we can modify it to allow the URL to accept `parameters` such as a zipcode to display only the tweets from a particular area.

```
def index
  if params[:zipcode]
    @tweets = Tweet.where(zipcode: params[:zipcode])
  else
    @tweets = Tweet.all
  end
  respond_to do |format|
    format.html
    format.xml { render xml: @tweets }
  end
end
```
To make this work, we can add `get '/local_tweets/:zipcode' => 'tweets#index', as 'local_tweets'`. Giving it a name `local_tweets` will allow us to link it using its path as seen here `<%= link_to "Tweets in 32828", local_tweets_path(32828) %>`.

It is easy create a Twitter-esque application where passing in a `username` as a parameter into the root URL (such as `/martiuslim`) brings you to the profile page of the user. Simply add `get ':name: => 'tweets#index', as: 'zombie_tweets'`.

```
def index
  if params[:name] 
    @zombie = Zombie.where(name: params[:name]).first
    @tweets = @zombie.tweets
  else
    @tweets = Tweet.all
  end
end
```