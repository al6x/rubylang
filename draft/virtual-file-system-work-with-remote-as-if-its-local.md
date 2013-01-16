# Virtual File System - work with remote as if it's local

Capistrano is great for it's purpose, but sometimes You need not only deployment (but also
install & manage things like nginx & mysql) and Your way is a little different than
the opinionated 'The Rails Way'. And in these situations using Capistrano may be a little difficult.

If this is the case, then maybe You will be interested in the general purposed tool to configure
and manage Your remote machine(es).

The [Vfs](https://github.com/alexeypetrushin/vfs) (Virtual File System) + Vos (Virtual Operating System) -
tools to eliminate difference between local and remote machine, it makes working with remote machine
as if it's local. You may also think of it as kind of ActiveRecord for File Systems (the AR uses
the same api for almost any database, the same principle is here).

A little code sample:

``` ruby
server = Box.new('cool_app.com')
me = '~'.to_dir

deploy_dir = server['apps/cool_app']
projects = me['projects']

# copying from local project to remote
projects['my_cool_app'].copy_to deploy_dir

# configuring database on remote machine
database_config = deploy_dir.file('config/database.yml')
database_config.write("user: root\npassword: secret")

# starting rails on remote machine
deploy_dir.bash 'rails production'
```

It's just a small part of it's API, please see more in docs below.

Tags : Administration, Deployment, SSH
Date : 2011/9/1
