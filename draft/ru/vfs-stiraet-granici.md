# Vfs - стирает разницу между локальным и удаленным ПС (деплоймент, shh, ...)

Capistrano классный инструмент, но иногда нужен не только деплоймент (а например установка и запуск всяких nginx & mysql) и иногда то что нужно несколько отличается от предвзятого "The Rails Way". В этих ситуациях использовать капистрано может быть немного проблематично. И в этом случае хотелось бы универсальный инструмент, а не предвзятый.

Собственно что мне и потребовалось где-то полгода назад, и первым делом я нашел такую потрясающую штуковину от Адамса Виггинса (одного из основателей Хероку, по видимому они ее у себя испольовали) как rush. Но, к сожалению Адамс забросил этот проект, к тому-же для работы rush необходим специальный запущенный процесс на удаленной машине.
Из других - Шеф - поднимать специальный сервер и разбираться в его рецептах только ради того чтобы собрать пару машин - как-то слишком, влад и иже с ним - тоже не то, большая часть из них них - это все те-же капистрано внутри.

Поэтому я сделал клон rush - Vfs (Virtual File System) абстракцию над файловой системой и частично терминалом которая позволяет работать с удаленной машиной точно также как и с локальной (по аналогии как ActiveRecord может работсть с разными базами данных но для вас он всегда один и тот-же)

Немного кода

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

Это небольгая часть API, подробнее можно посмотреть по ссылкам ниже.
Я также использую его для развертывания своих проектов, например этого http://ruby-lang.info

Собственно сам проект:
https://github.com/alexeypetrushin/vfs

Немного документации:
http://ruby-lang.info/blog-ru/virtualnaia-failovaia-sistiema-ven
http://ruby-lang.info/blog-ru/prostoi-instrumient-upravlieniia-konfighuratsiiei-k-ltl

Пример моей рабочей конфигурации сервера:
https://github.com/alexeypetrushin/my_cluster

Tags : SSH, Администрирование