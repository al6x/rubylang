# Виртуальная файловая система (Local, S3, SFTP)

[Виртуальная файловая система][vfs] позволяет использовать простой и одинаковый API для работы с различными провайдерами (локальной файловой системой, AWS S3, SFTP).

I/O:

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

API одинаков для любого провайдера, скопируем ассеты приложения на S3:

``` ruby
box = Box.new aws_credentials
app['public'].copy_to box['/']
```

Тоже самое для SFTP, деплоймент приложения:

``` ruby
server = Box.new 'app.com'
app.copy_to server['/apps/app']
server['/apps/app'].bash 'server start'
```

Примеры и документация: http://alexeypetrushin.github.com/vfs

``` bash
gem install vfs
```

[vfs]: http://alexeypetrushin.github.com/vfs

Tags : S3, SSH, Администрирование