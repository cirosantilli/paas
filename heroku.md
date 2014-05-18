PaaS: Heroku is not a fully blown image + SSH server.

While you can ssh into it via `heroku run bash`, you cannot use `sudo`, and therefore you cannot install anything that is not meant to be installed.

It only supports applications on certain programming languages such as Ruby or Python.

The application type is detected by the presence of certain files relative to the root git directory. For example:

- `Gemfile` + `Gemfile.lock` indicates Ruby
- `config.ru` indicates rack
- `setup.py` Python

Heroku also imposes further restrictions such as:

-   no MySQL, only PostgreSQL. Not all operations are allowed to be done.

    Database creation and drop for example are only allowed as side effect of certain `heroku` commands, not with a general `run`. Use the `pg` commands for those actions.

Heroku deploys with git. After a `git push heroku master` post-update hooks to the rest of the job for you.

#foreman

Heroku recommends that you use foreman to deploy.

#heroku command

Login on local machine into Heroku:

    heroku login

Only needs to be done once.

Create remote Heroku repo and add it as a remote named `heroku` to the current git repo:

    heroku create

This app will have a randomly generated name. To choose a name use:

    heroku create appname

The app will appear under:

    <appname>.herokuapp.com

Upload and download is done via Git.

On push the app should deploy automatically:

    git push heroku master

View server status:

    heroku ps

The following output shows that one dyno is running:

    === web (1X): `gunicorn main:app`
    web.1: up 2013/11/05 21:52:29 (~ 20m ago)

Get the servers working for an web app with one dyno:

    heroku ps:scale web=1

Get the servers working for an web app with 2 dynos (would exceed free month allowance):

    heroku ps:scale web=2

Open app in default browser:

    heroku open

View event log:

    heroku logs

Run a command remotely:

    heroku run cmd

Sample commands:

    heroku run python           # remote python shell
    heroku run rake db:migrate  # db migration

Useful for one-off commands.

Start a worker thread:

    heroku ps:scale worker=1

Get info on app in current dir:

    heroku info

Sample output:

    === intense-lake-1928
    Git URL:       git@heroku.com:intense-lake-1928.git
    Owner Email:   ciro.santilli@gmail.com
    Region:        us
    Repo Size:     109M
    Slug Size:     28M
    Stack:         cedar
    Tier:          Legacy
    Web URL:       http://intense-lake-1928.herokuapp.com/

Destroy app with given name:

    heroku destroy appname

This will prompt for you to write the appname to confirm.
To destroy it directly do:

    heroku destroy --confirm appname

##config

Set and get environment variables.

These changes are persistent despite the ephemeral filesystem.

Every time a variable changes the application restarts.

View config vars:

    heroku config

Does *not* show all environment variables, only those which are persistent configs.

    heroku config:set KEY=val
    heroku config:get KEY
    heroku config:unset KEY

Some of the configurations have effects on the Heroku operation.

    heroku config:add LOG_LEVEL=DEBUG

#database

See pg.

#pg

Heroku runs on PostgreSQL.

It seems that a database is created for every app.

All the database info can be found on the environment variable `DATABASE_URL`.

In Django, this is parsed and fed to Django via the Python package `dj_database_url`.

It is not possible to view your databases via `heroku run rake db`.

You must use the `heroku` command `pg`.

View DB info:

    heroku pg:info

Reset database (only drop + create, no `schema:load` + `seed`):

    heroku pg:reset

Note that it is not possible to use `heroku run rake db:drop` nor `heroku run rake db:create`.

#ephemeral filesystem

While the dyno runs, it can read and write to the filesystem normally.

However:

- other dynos cannot see those changes
- when the dyno stops those changes are discarded

This means that there are things which you simply cannot do:

- file uploads

Therefore, the only way to modify data forever is through:

- git uploads at deployment
- database modification

A solution is to store data on an external server such as Amazon S3.

#rails

What you need to do to get a rails app running:

-   put everything on the git root

-   on the `Gemfile` use `gem pg`, and not any other database gem

    After that do `bundle install` to update the `Gemfile.lock`

-   on `config/database.yml` use only

        development:
          adapter: postgresql
          encoding: unicode
          database: myapp_development
          pool: 5
          password:

    No need to define production settings, those are added automatically.

-   use the following `config/unicorn.rb`:

        worker_processes Integer(ENV["WEB_CONCURRENCY"] || 3)
        timeout 15
        preload_app true

        before_fork do |server, worker|
        Signal.trap 'TERM' do
            puts 'Unicorn master intercepting TERM and sending myself QUIT instead'
            Process.kill 'QUIT', Process.pid
        end

        defined?(ActiveRecord::Base) and
            ActiveRecord::Base.connection.disconnect!
        end

        after_fork do |server, worker|
        Signal.trap 'TERM' do
            puts 'Unicorn worker intercepting TERM and doing nothing. Wait for master to send QUIT'
        end

        defined?(ActiveRecord::Base) and
            ActiveRecord::Base.establish_connection
        end

-   The usual:

        heroku create rails-test
        git add .
        git push heroku master
        heroku run rake db:schema:load
        heroku run rake db:seed
        heroku ps:scale web=1
        heroku open

You cannot use `db:setup`, because that runs `db:create`, and direct db creation is not allowed! Same for `db:drop` and `db:reset`.
