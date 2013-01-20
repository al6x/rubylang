# Вы недооцениваете мощь IoC

!![icon] Ходят в Ruby сообществе слухи о **IoC**. Восновном о том как кто-то попытался использовать его, у него ничего не получилось и теперь он пытается убедить других в том что IoC это плохо, это "не путь Ruby" и вообще "все это уже и так в строено в самом языке".

Использую IoC как центральный компонент для фреймворка Rad (на котором работает этот сайт) и это очень сильно упростило мне работу.

Думаю, большинство людей критикующих IoC пришло из мира Java и они пытаются применить известные им жава техники к Ruby. И, сюрприз - получается как-то не очень.

Одно из самых больших достоинств в Java от IoC получается от использования AOP техник (что-то вроде метапрограммирования) - и, поскольку все это и так есть в Ruby - можно прийти к неверному заключению что IoC тут совсем не нужен.

Но это неверно, это всего-лишь одно из его применений, у него есть также и другие, не менее важные:

- **Автоматическое управление зависимостями** - автоматическое - означает что **вам вообще не нужно использовать 'require'**, ваши классы и компоненты автоматически находятся, загружаются, инициализируются и собираются вместе.
Если вы используете 'require' и собираете их явно - вы используете IoC неверно!

- **Управление конфигурациями** - в коде не должно быть явного использования конфигураций, переменные из конфигурационных файлов автоматически вставляются туда куда нужно.
Если вы явно используете конфиг - вы использете IoC неверно (иногда приходится конечно явно это делать, но большую часть времени все это делается автоматически)!

- Управление как Статическими так и **Динамическими** компонентами - по какой-то странной причине все Ruby IoC которые я видел собирают только статическую структуру приложения и совершенно игнрируют динамические обьекты (например создание нового контроллера для каждого запроса).
Так не должно быть, IoC должен управлять как статическими так и динамическими компонентами.

- 99% времени **IoC скрыт и невидим**, если вам приходится учить его API или видеть его в вашем коде - вы используете его неверно!

А использование IoC без этих техник - равносильно попытке пилить выключенной бензопилой.

Сложно придумать маленький пример который бы явно доказал все преимущества IoC, потому что они начинают проявлятся тогда когда ваше приложение становится большим. Тогда становится заметным то преимущество которое он дает - ваш код остается понятным и чистым а не превращается в монолит или кучу запутанных зависимостей.

## Пример

Ниже небольшой пример [Micon][micon] IoC (управление зависимостями не показано, пример слишком простой). Примеры реального кода будут в статьях позже.

``` ruby
require 'micon'

# Here's our Web Framework, let's call it Rad

# Let's define shortcut to access the IoC API (optional
# but handy step). I don't know how You would like to call it,
# so I leave this step to You.
class ::Object
  def rad; MICON end
end

# let's define some components
# the :logger is one per application, it's a static component (like singleton)
class Logger
  register_as :logger
  attr_accessor :log_file_path
  def info msg
    puts "#{msg} (writen to #{log_file_path})" unless defined?(RSpec)
  end
end

# To demostrate basics of working with compnents let's configure our :logger
# explicitly (in the next example, it will be configured automatically).
rad.logger.log_file_path = '/tmp/rad.log'

# The :router requires complex initialization, so we use
# another form of component registration.
class Router
  def initialize routes; @routes = routes end
  def decode request;
    class_name, method = @routes[request.url]
    return eval(class_name), method # returning actual class
  end
end
rad.register :router do
  Router.new '/index' => ['PagesController', :index]
end

# The :controller component should be created and destroyed dynamically,
# for each request, we specifying that component is dynamic
# by declaring it's :scope.
# And, we don't know beforehead what it actully will be, for different
# request there can be different controllers,
# so, here we just declaring it without any initialization block, it
# will be created at runtime later.
rad.register :controller, scope: :request

# Let's define some of our controllers, the PagesController, note - we
# don't register it as component.
class PagesController
  # We need access to :logger and :request, let's inject them
  inject logger: :logger, request: :request

  def index
    # Here we can use injected component
    logger.info "Application: processing #{request}"
  end
end

# Request is also dynamic, and it also can't be created beforehead.
# We also registering it without initialization, it will be
# created at runtime later.
class Request
  attr_reader :url
  def initialize url; @url = url end
  def to_s; @url end
end
# Registering without initialization block.
rad.register :request, scope: :request

# We need to integrate our application with web server, for example with the Rack.
# When the server receive web request, it calls the :call method of our RackAdapter
class RackAdapter
  # Injecting components
  inject request: :request, controller: :controller

  def call env
    # We need to tell Micon that the :request scope is started, so it will know
    # that some dynamic components should be created during this scope and
    # destroyed at the end of it.
    rad.activate :request, {} do
      # Here we manually creating the Request component
      self.request = Request.new '/index'

      # The :router also can be injected via :inject,
      # but we can also use another way to access components,
      # every component also availiable as rad.<component_name>
      controller_class, method = rad.router.decode request

      # Let's create and call our controller
      self.controller = controller_class.new
      controller.send method
    end
  end
end

# Let's pretend that there's a Web Server and run our application,
# You should see something like this in the console:
#   Application: processing /index
RackAdapter.new.call({})
```

Tags : DI, IoC, Сложность

[micon]: https://github.com/alexeypetrushin/micon
[icon]: https://firenet.s3.amazonaws.com/fs/4da14c38743f0f218d00000f/4da18816743f0f2c31000015/4e096443743f0f489a000038/dark%20side.thumb.jpg