# View
The `Visual Representation` of the application

Following the `zombie_twitter` tutorial, our current application directory will look like
```
- zombie_twitter
    |- app
        |- views
            |- layouts
                |- application.html.erb
            |- zombies
            |- tweets
                |- index.html.erb
                |- show.html.erb
```
`erb` stands for ~~edible rotting boddies~~ embedded ruby, basically having ruby code inside a html file.

To show an individual `tweet`, we will look at `/app/views/tweets/show.html.erb` and it will look like the following where we will use the `Tweet` model to fetch a `tweet` with an id of 1.
```
<% tweet = Tweet.find(1) %>
<h1><%= tweet.status %></h1>
<p>Posted by <%= tweet.zombie.name %></p>
```
`<% ... %>` tells Rails to evaluate ruby code found within the angular brackets.
`<%= ... %>` tells Rails to evaluate ruby code and print the result.

Place template/ boilerplate code inside `/app/views/layouts/application.html.erb` and every page created will use this template by default. Use `yield` to show where the contents of the particular page go, i.e. the code inside `show.html.erb`.

```
<!DOCTYPE html>
<html>
  <head><title>RailsForZombies</title></head>
  <body>
    <header>...</header>
    <%= yield %>
  </body>
</html>
```
Create a link using `link_to`, recipe: `<%= link_to text_to_show, model_instance %>  
Example: `<%= link_to tweet.zombie.name, zombie_path(tweet.zombie) %>` or `<%= link_to tweet.zombie.name, tweet.zombie %>`.

Add a prompt using `confirm`, example:
```
<%= link_to tweet.zombie.name,
    tweet.zombie,
    confirm: "Are you sure?" %>
```

To show all `tweets`, we edit `/app/views/tweets/index.html.erb`. Here, `Tweet.all` returns an array which we store in `tweets`. We then loop through each `tweet` to populate the table with links to each `tweet` by each `zombie`. This also allows the zombies to `edit` or `delete` their tweets.
```
<% tweets = Tweet.all %>
<% tweets.each do |tweet| %>
  <tr>
    <td><%= link_to tweet.status, tweet %></td>
    <td><%= link_to tweet.zombie.name, tweet.zombie %></td>
    <td><%= link_to "Edit", edit_tweet_path(tweet) %></td>
    <td><%= link_to "Destroy", method: :delete %></td>
  </tr>
<% end %>
<% if tweets.size == 0 %>
  <em>No Tweets Found</em>
<% end %>
```

## URL Generator Methods
Link recipe: `<%= link_to text_to_show, code %>`  
The last 3 paths need a specific `tweetID` such as `tweet = Tweet.find(1)`  

| Action | Code | The URL |
| ------ | ---- | ------- |
| List all tweets | tweets_path | /tweets|
| New tweet from | new_tweet_path | /tweets/new|
| Show a tweet | tweet | /tweets/1|
| Edit a tweet | edit_tweet_path(tweet) | tweets/1/edit|
| Delete a tweet | tweet, method: :delete | /tweets/1|
