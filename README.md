# ErrandBoy [![CircleCI](https://circleci.com/gh/bm-sms/errand_boy.svg?style=shield&circle-token=79a65d3d5cdad3c9536ae7d25ddd462093267b9d)](https://circleci.com/gh/bm-sms/errand_boy)

ErrandBoy is the admin support tool.


## Description

ErrandBoy helps a administrator job. It executes the action when administrator merged pull request.
If you want to request for slack invitation to administrator, all it takes is to write a request file and create pull request to the ErrandBoy repository.
When the administrator merge pull request that allowed by him, ErrandBoy sends slack invitation mail instead of the administrator.


## Installation

### \* See [errandboy_example](https://github.com/bm-sms/errand_boy/tree/master/example)

#### Add this line to your application's Gemfile:

```ruby
gem 'errand-boy'
```

#### Add `index/` and `requests/`:

```shell
$ mkdir ./index
$ mkdir ./requests
$ touch ./requests/.keep
```

#### Add `bin/exe`:

```ruby
#!/usr/bin/env ruby

require 'bundler/setup'
require 'errand_boy'

ErrandBoy::Brain.new.command
```

#### Add circle.yml:

```yml
machine:
  timezone:
    Asia/Tokyo

  ruby:
    version:
      2.3.1

  post:
    - sudo service mysql stop
    - sudo service postgresql stop

dependencies:
  pre:
    - gem install bundler -v 1.11

  override:
    - bundle install

test:
  override:
    - echo "-----no test-----"

deployment:
  run:
    branch: /master|.*request.*/
    commands:
      - git config --global user.name errand-boy
      - git config --global user.email errandboy0.1.0@gmail.com
      - bin/exe
```

## Setting

### Slack

#### Get a slack access token from https://api.slack.com/web:

[![generate_test_tokens](https://github.com/bm-sms/errand_boy/wiki/images/generate_test_tokens.png)](https://api.slack.com/web)

#### Create access token:

[![create_token](https://github.com/bm-sms/errand_boy/wiki/images/create_token.png)](#)

### CircleCI

#### Build CircleCI project from https://circleci.com/add-projects:

[![build_project](https://github.com/bm-sms/errand_boy/wiki/images/build_project.png)](https://circleci.com/add-projects)

#### Registered a deploy key:

place: Project Settings >> Permissions >> Checkout SSH keys

[![authorize_w](https://github.com/bm-sms/errand_boy/wiki/images/authorize_w.png)](#)

#### Add slack access token to environment variable:

place: Project Setting >> Build Settings >> Environment Variables

[![environment_variable](https://github.com/bm-sms/errand_boy/wiki/images/environment_variable.png)](#)


## Usage

Make and checkout a new branch that named including `request` (prefix):

```shell
$ git checkout -b new_request
```

Make a new request file in `requests/` folder:

- The file should be formatted by [TOML](https://github.com/toml-lang/toml).

```shell
$ vim requests/example.toml
```

```toml
# requests/example.toml

provider    = 'slack'  # Now slack only.
action      = 'invite' # Now invite only.
sender      = { name = 'tanaka taro', email = 'taro@example.com' } # Sender information.
# Register information. It is required information.
register  = { name = 'yamada hanako', email = 'hanako@example.com' }
destination = { team = 'example' } # It is team that the register will be invited.
```

Push a new request file and create a pull request:

- Push a new request file

```
$ git add .
$ git commit -m 'request slack invite example'
$ git push -u origin new_request
```

- Create a new pull request on your `errand_boy` repository.

Merge pull request by administrator:

ErrandBoy executes the action when the administrator merge pull request.


### Confirm action histories

If you want to see invite histories, you can see `index/***.csv` on your `errand_boy` repository.


## ENV

```
SLACK_ACCESS_TOKEN - You should set slack access token on CircleCI.
```

## Contributing

Bug reports and pull requests are welcome on GitHub at https://github.com/bm-sms/errand_boy.


## License

The gem is available as open source under the terms of the [MIT License](http://opensource.org/licenses/MIT).
