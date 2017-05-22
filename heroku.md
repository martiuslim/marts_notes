# Heroku

## Useful Links
- [Heroku Architecture](https://devcenter.heroku.com/categories/heroku-architecture)
- [Heroku CLI](https://devcenter.heroku.com/articles/heroku-cli#standalone-version)
- [Heroku Deployment](https://devcenter.heroku.com/categories/deployment)
- [Heroku Collaboration](https://devcenter.heroku.com/articles/collab)

## Useful Commands

- To clone the source of an existing application from Heroku using Git, use the `heroku git:clone` command: `heroku git:clone -a <myapp>`.
- To add the canonical source code repository for the application, use the `heroku git:remote` command.
- To deploy the application, commit using `git commit -a -m "<description of changes made>"` and push using `git push heroku master`.
- To open the application, use `heroku open`.