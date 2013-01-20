# Виртуальная файловая система

Небльшая абстракция над любой файловой системой или базой которые имеют понятия Файл и Директория. Грубо говоря Vfs для файловой системы это то-же самое что ActiveRecord для реляционной базы.

В данный момент доступны следущие хранилища:

- локальна файловая система
- удаленная файловая система (через ssh)

## Цели

- **удобный, маленкий и простой** API.
- **высокая производительность** - точно такая-же как и при использовании файловой системы напрямую, дополнительных вызовов или проверок быть не должно **.
- один и тот-же API для разных систем (локальная, SSH, Amazon S3, другие ...).
- **одновременно работать с разными системами и обмениваться данными между ними**.
- маленький размер, исходники должны быть легко доступны для понимания и модификации сторонними разработчиками.
- очень простая реализация драйверов к файловым системам, должно быть просто добавлять новые типы хранилищ (Hadoop DFS, LDAP, Document Oriented DB, In-Memory, ...).

** все методы должны работать так-же быстро как и низкоуровневые, за исключением :move и :rename. Сейчас они по быстрому сделаны как copy+destroy, как появится время я их переделаю по нормальному.

## Примеры:

``` ruby
gem 'vfs'                                    # Виртуальная файловая система
require 'vfs'

gem 'vos'                                    # Виртуальная операционая система
require 'vos'


# Установление соединения, задеплоим наш 'cool_app' с локального на удаленный сервер
server = Box.new('cool_app.com')             # он использует id_rsa, или вы можете
                                             # дбавить {user: 'me', password: 'secret'}

me = '~'.to_dir                              # удобное сокращение для локальной файловой системы

deploy_dir = server['apps/cool_app']
projects = me['projects']


# Работа с папками, копирование папки/файла с любого хранилища в любое
# другое (локальное/удаленное/другой_тип)
projects['cool_app'].copy_to deploy_dir


# работа с файлами
dbc = deploy_dir.file('config/database.yml') # <= папка 'config' еще не существует
dbc.write("user: root\npassword: secret")    # <= сейчас 'database.yml' и
                                             # родительская папка 'config' были созданы
dbc.content =~ /database/                    # => false, мы забыли добавить database в конфиг
dbc.append("\ndatabase: mysql")              # поправим это

dbc.update do |content|                      # и добавим информацию о хосте
  content + "\nhost: cool_app.com "
end

projects['cool_app/config/database.yml'].    # или можно просто перезаписать удаленный конфиг
  copy_to! dbc                               # локальной версией

# также есть работа с потоками (чтение/запись/добавление) с использованием &block,
# детали смотрите в сецификациях


# Проверки
deploy_dir['config'].exist?                  # => true
deploy_dir.dir('config').exist?              # => true
deploy_dir.file('config').exist?             # => false

deploy_dir['config'].dir?                    # => true
deploy_dir['config'].file?                   # => false


# Навигация
config = deploy_dir['config']
config.parent                                # => </apps/cool_app>
config['../..']                              # => </>
config['../..'].dir?                         # => true

deploy_dir.entries                           # => файлы и директории, можно передать &block
deploy_dir.files                             # => файлы, можно передать &block
deploy_dir.dirs                              # => директории, можно передать &block

# дополнительные примеры использования смотрите в спецификациях (создать/обновить/переместить/
# копировать/удалить/...)
```

# Зачем?

Чтобы упростить себе работу: с локальной файловой системой, удаенной системой (администрация кластера, деплоймент) и для работы со специфичными системами типа Amazon S3.

Потому что API стандартной библиотеки File/Dir/FileUtils на мой взгляд неудобный. И по моему для этого есть причина - их назначение - предоставить 1-в-1 клон низкоуровневого API операционной системы, вместо того чтобы обеспечить удобный инструмент для работы.

А когда вы начинаете работать с удаленной файловой системой все становится еще запутанней (Net::SSH & Net::SFTP используют API немного отличающийся от локального, и приходится запоминать все эти мелкие отличия и недочеты).


## Интеграция с [Vos][vos] (виртуальной операционной системой)

``` ruby
server['apps/cool_app'].bash 'rails production'
```

Детального описания смотрите на странице проекта [Vos][vos].

Вы также можете посмотреть пример конфигурации для управления удаленным сервером [My Cluster][my_cluster] использующей дополнительно еще одну маленькую утилиту [Cluster Management][cluster_management].

## Установка

``` bash
gem install vfs
gem install vos
```

исходники: [http://github.com/alexeypetrushin/vfs][vfs]

## Будущее

- добавить новые типы хранилищ: Hadoop DFS, MongoDB, Amazon S3

Tags : IO, SSH, Администрирование, Проект

[vfs]: https://github.com/alexeypetrushin/vfs
[vos]: http://github.com/alexeypetrushin/vos
[cluster_management]: http://github.com/alexeypetrushin/cluster_management
[my_cluster]: http://github.com/alexeypetrushin/my_cluster