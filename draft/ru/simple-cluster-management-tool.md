# Простой инструмент управления конфигурацией кластера

Простой инструмент управления конфигурацией кластера

Этот инструмент может быть полезным если у вас имеется порядка 1-10 машин и инструменты типа Chef, Puppet, Capistrano слишком сложные или не подходят для ваших задач.
Он **очень простой**, всего 2 компонента - Box и Service.

Пример ы использования:

- инсталяция пакетов, зависимости, версии
- управление процессами, запуск/остановка/рестарт
- деплоймент, развертывание приложения

Предназнаячен для использования вместе с [Virtual File System][vfs], [Virtual Operation System][vos] и Rake, но это не жесткое требование, вы можете использовать также другие инструменты.

## Концепция

- Box - компютер с удаенным доступом.
- Service - абстрактная вещ (пакет, запущенный процесс, приложение, ...) которая находится на 1..n машинах. Service может выполнять операции типа install/update/start/stop/.. и взаимодействовать с другими сервисами.
- Deployment Scheme - определяет какие сервисы находятся на каких машинах.

## Deployment Scheme

!![deployment_scheme] Предположим нам нужно развернуть приложение на 3х машинах используя следущую схему. **Тэги** используются для определения связей (N к M, хотя в этом примере простой случай 1 к 1) между **машинами** и **Сервисами**.

## Машины

Машины определяются в конфиге:

``` yaml
handy_scheme:
  'web1.app.com': ['web']
  'web2.app.com': ['web']
  'db.app.com': ['db']
```

## Сервисы

Зависимости между сервисами определяются явно - через вызовы других серсивов, такой подход позволяет высокую гибкость и при этом очень прост для понимания и использования.

И, он достаточно умен, например в примере ниже метод App::deploy понимает что прежде чем установить App ему нужны Сервисы Ruby и он установит и запустит их прежде чем начать деплоймент приложения.

Вы также можете определить чтобы пакет был установлен только один раз (см. :apply_once), и использовать версии (см. :version) - обносите версию - и сервис также будет обновлен.

Инструмент подерживает пошаговую разработку и понимает какие сервисы отсутсвуют и должны быть установлены, вам не нужно сразу писать всю конфигурацию от начала и до конца, делайте это небольшими шагами, постепенно добавляя новые сервисы.

Ниже конфигурация серсивов для нашего приложения:

``` ruby
class Services::Ruby < Service
  tag :web
  version 4

  def install
    # it will be called only once, it will be called next time only if You change the version
    apply_once :install do |box|
      logger.info "installing :#{service_name}"
      box.fake_bash "apt-get install ruby"
    end
  end
end

class Services::Thin < Service
  tag :web

  def restart
    logger.info "restarting :#{service_name}"
    boxes.each{|box| box.fake_bash 'thin restart'}
  end
end

class Services::MySql < Service
  tag :db
  def started
    logger.info "ensuring mysql is running"
    box.fake_bash 'mysql start' unless box.fake_bash('ps -A') =~ /mysql/
  end
end

class Services::App < Service
  tag :web

  def install
    services.ruby.install
    apply_once :install do |box|
      logger.info "installing :#{service_name}"
      box.fake_bash "cd #{config.app_path} && git clone app"
    end
  end

  def update
    install
    logger.info "updating :#{service_name}"
    boxes.each{|box| box.fake_bash "cd #{config.app_path} && git pull app"}
  end

  def deploy
    update
    logger.info "deploying :#{service_name}"
    services.my_sql.started
    services.thin.restart
  end
end
</code>

## Rake

<code language='ruby'>
desc 'deploy to cluster'
task :deploy do
  cluster.services.app.deploy
end
```

Примечание: Вам не нужно вручную вызывать установку сервисов перед вызовом команды деплоймента, команда **App::deploy автоматически выяснит все зависимости и полностью сконфигурирует все машины с нуля** - проинсталирует нужные пакеты, запустит сервисы и убедится что они работают и только потом начнет установку приложения.

Наберите в командной строке:

``` bash
$ rake deploy
```

И вы увидите что-то вроде:

``` bash
installing :ruby
   => bash: 'apt-get install ruby'
installing :app
   => bash: 'cd /tmp/cm_example_app && git clone app'
updating :app
   => bash: 'cd /tmp/cm_example_app && git pull app'
deploying :app
ensuring mysql is running
   => bash: 'ps -A'
   => bash: 'mysql start'
restarting :thin
   => bash: 'thin restart'
```

Исполните команду еще раз, заметьте теперь он пропустил установку Ruby и App:

``` bash
updating :app
   => bash: 'cd /tmp/cm_example_app && git pull app'
deploying :app
ensuring mysql is running
   => bash: 'ps -A'
   => bash: 'mysql start'
restarting :thin
   => bash: 'thin restart'
```

## Установка

``` bash
$ gem install cluster_management
```

## Примеры

Скачайте [cluster_management][cluster_management] и перейдите в папку примера [example][example], наберите 'rake deploy' и посмотрите на вывод.

Для простоты пример использует один 'localhost' вместо 3х удаленных серверов и 'fake_bash' который просто печатает команды на консоль но не испольняет их (потому что нам не нужно на самом деле что-то устанавливать на локалхосте).
Но вы можете легко поменять конфиг и задать там адреса настоящих удаленных машин и заменить 'fake_bash' на 'bash' и посмотреть как он работает на самом деле.

Вы также можете посмотреть пример "реальной" конфигурации которую я использую для управления и деплоймента этого сайта [my_cluster][my_cluster].

Tags : SSH, Администрирование

[my_cluster]: http://github.com/alexeypetrushin/my_cluster/tree/master/lib/packages
[vos]: http://github.com/alexeypetrushin/vos
[vfs]: http://github.com/alexeypetrushin/vfs
[deployment_scheme]: https://firenet.s3.amazonaws.com/fs/4da14c38743f0f218d00000f/4da18816743f0f2c31000015/4e159217743f0f28780000d0/deployment_scheme.thumb.png
[example]: https://github.com/alexeypetrushin/cluster_management/tree/master/example
[cluster_management]: https://github.com/alexeypetrushin/cluster_management