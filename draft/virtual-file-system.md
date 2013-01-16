# Virtual File System

[Virtual File System](http://alexeypetrushin.github.com/vfs) provides clean, simple and
unified API over different storages (Local File System, AWS S3, SFTP).

I/O operations:

``` ruby
home['projects/app'].files                       # => ['Rakefile', 'readme']

readme = home['projects/app/readme']
readme.copy_to other_project['readme']
readme.move_to home['Desktop']

readme.write 'My Cool App'
readme.update{|text| text.gsub /App/, 'App v2'}
readme.read                                      # => "My Cool App v2"

app['**/*.rb']                                   # => [list of *.rb files]

app['**/*.rb'].collect(&:size).reduce(:+)        # => sources size
```

Same API on any storage type, let's copy application assets to S3:

``` ruby
box = Box.new aws_credentials
app['public'].copy_to box['/']
```

Same API for SFTP, deploying our App:

``` ruby
server = Box.new 'app.com'
app.copy_to server['/apps/app']
server['/apps/app'].bash 'server start'
```

More samples: http://alexeypetrushin.github.com/vfs

``` bash
gem install vfs
```

Tags : File System, Administration, S3, SSH
Date : 2011/11/1