# chiscore

[![Build Status](https://travis-ci.org/chiditarod/chiscore.svg?branch=master)](https://travis-ci.org/chiditarod/chiscore)
[![Code Climate](https://codeclimate.com/github/chiditarod/chiscore/badges/gpa.svg)](https://codeclimate.com/github/chiditarod/chiscore)
[![Test Coverage](https://codeclimate.com/github/chiditarod/chiscore/badges/coverage.svg)](https://codeclimate.com/github/chiditarod/chiscore/coverage)

_Timekeeping and Scoring application for the CHIditarod._

## Requirements

- Redis
- Ruby (`2.5.0`)
- Node.js (`6.13.0`, `8.9.4`)

## Architecture

chiscore consists of client and server components.  The server is
written in Ruby and uses Sinatra.  The client-side is Node.js.

The application requires a secret key and an admin key in order to
function.  These keys can easily be generated using the included rake
task, `rake gen_secrets`.  This will place the keys in the `config`
folder and the app will automatically consume them.

Alternatively, and to support Cloud hosting services like Heroku, the
`SECRET_KEY` and `ADMIN_KEY` environment variables can be provided in
leiu of the files on disk.

## Developer Setup

_Assumes you are using OSX. Pull requests for other setups gladly accepted._

### Setup Daemons and Environment

- Install [homebrew](http://brew.sh/).
- [Install rbenv](https://github.com/rbenv/rbenv#homebrew-on-mac-os-x) using homebrew.
- Install prequisites and clone the code:

```bash
brew install ruby-build
rbenv install $(cat .ruby-version)
git clone github.com:chiditarod/chiscore
cd chiscore
```

Boot local docker
```
docker-compose up -d redis
```

### Install

#### MacOS 12.6

```
xcode-select --install
softwareupdate --all --install --force

brew install readline openssl ruby-build
brew install redis # skip if using docker
rbenv install 2.7.5
gem install bundler
bundle install
bundle exec rake gen_secrets     # generate secret keys
```

### Client Setup

You need node.js for compilation and running of JavaScript specs.

```bash
brew install node
npm install -g n           # n is an easy node version switcher
npm install
# sudo n 8.9.4               # install node version 8.9.4, for example
# yarn
```

### Development Login

- Use a username and password from `config/data/$year/logins.csv`
- Use a team number from `config/data/$year/teams.csv`

## Deployment to Heroku

Do these steps every year.

- Copy `config/data/2023/*` to `config/data/2024/` (change the years accordingly)
- Customize the new files as necessary.
- Modify `app.rb` and update the year.
- Do not submit the new files as a PR.  Do not push them to GitHub otherwise you will expose the passwords.

If copying the directory outright, change the old passwords before pushing to GitHub.


- Generate the admin and secret keys:

        bundle exec rake gen_secrets

- Set the following Heroku Config Vars:

    - `ADMIN_KEY` - find in `config/` after running `bundle exec rake gen_secrets`
    - `SECRET_KEY` - find in `config/` after running `bundle exec rake gen_secrets`
    - `TZ` - Set the app timezone.  See the `TZ` column in [the list](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones)
    - `REDIS_URL` - Example: `redis://redistogo:abc123abc12381276e68463f5b9d4764@foo.redistogo.com:11442/`

- Add the heroku remote if needed:

        heroku git:remote -a chiscore1

- Deploy the application:

        git push heroku main
        git push heroku db/nopr-2024:main

## Usage Examples

### Server

Start redis:

    docker-compose up -d redis
    # or 'redis-server', etc

Start the server:

    bundle exec rackup

Run the ruby unit test suite:

    bundle exec rake

### Client

Run the frontend tests:

    grunt spec

Compile coffee and EJS templates:

    grunt build

Watch and compile coffee and EJS templates:

    grunt watch

Clean:

    grunt clean

## Redis Operations

### Restore

___Caution: Destructive___

    docker-compose up -d redis
    docker cp dump.rdb $(docker-compose ps -q redis):/data/dump.rdb
    docker-compose restart redis

### Flush Redis DB

___Caution: Destructive___

    rake redis_flushdb # local
    heroku run bundle exec rake redis_flushdb

### Manually add a checkin

Get timestamp from linux shell
```
$ date +%s
1706169136
```

Open redis console
```
$ redis_cli
$ heroku redis:cli -a $app_name

> hset "checkins:$login_id" $team_id $timestamp
> hset "checkins:6" 1617 1706169136
```

## Export

The finish line checkpoint ID still has to be hard-coded; see `lib/chiscore/support/data_exporter.rb`, then look for `def finish_checkpoint` and update the integer ID to match the ID used for the finish line in `config/data/checkpoints.csv`.

```bash
YEAR=2018 OUTPUT=html SUMMARY=true rake export   # export nice HTML table lines
YEAR=2018 OUTPUT=csv rake export                 # export all timing data from redis
```

Run the export from Heroku:

```bash
heroku run -a chiscore1 YEAR=2020 OUTPUT=html rake export
```

