chiscore
============

## Installation
You need redis.

`brew install redis` and follow those instructions

You need bundle, and it needs to install some gems!

`gem install bundler && bundle `

You need node.js for compilation and running of JavaScript specs

`brew install node` if you don't has it,
then `npm install -g grunt-cli` -- the grunt-cli may require sudo.

Then, from the root `chiscore` directory, `npm install`

## Setup and Server
Navigate to the  root of the `chiscore` directory:

`cd chiscore`

Run configuration tasks:

`rake project_setup` (from application root)

Start a local server:

`unicorn` (or `rackup` if you're into that)

Start the Redis server:

`redis-server` (in a new tab or window)

Run test suite:

`rake spec_all` (runs ruby and js suites)

## Other Stuff
Run the Ruby spec suite only:

`rake spec`

Run the JavaScript spec suite only:

`grunt spec`

Compile coffee and EJS templates:

`grunt build`

Watch and compile coffee and EJS templates:

`grunt watch`

Remove compiled JS targets:

`grunt clean`

Logging in: dev environment
- use test-checkpoint / secret
- use a number between 1 and 160 to check in a team
