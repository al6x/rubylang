

!![icon] There are rumors in Ruby community about **Dependency Injection**. Mainly about how someone tried it, failed and now trying to convince everyone else that DI is bad, it's "not a Ruby way" and the "Ruby itself has an DI capabilities".

I use Micon DI as a heart of Rad Web Framwork (this site powered with it), and it tremendously simplify my work.

One of the biggest advantage of IoC / DI in Java comes from it's AOP technics (kind of Metaprogramming) - and because Ruby already have all this stuff - it's easy to be mislead to thinking that there's no need for DI.

But this is wrong, there are other, no less important advantages of DI:

- **Automatic Dependency Resolving**: automatic - means that **there should be no any 'require' at all**, all Your Classes and Components should be automatically located, loaded, initialized and wired together.
If You explicitly use 'require' and initialization to manage pieces of Your App with DI - You are using it wrong!

- **Configuration Management**: there should be no explicit 'config' usage in Your code, all variables from config files should be injected automatically into right places of Your App.
If You explicitly use :config with DI - You are using it wrong (well, sometime You has to use it, but most of time it works automatically and stays invisible)!

- Static and **Dynamic** Component Life-Cycle Management - for some strange reason all of Ruby DI I've seen worked only with Static Components (Singletons). It assembles static structure of App and completelly ignores dynamic objects (like creating new Controller per request).
That's wrong, DI should manage Dynamic Components as well.

- 99% of time **DI is silent and invisible**, if You are forced to learn DI API or see it in Your code - You are using it wrong!

And using DI without this stuff is like to trying to saw with the chainsaw turned off.

It's hard to show a small code example that will ultimately proove the advantages of DI, because it shines most when Your app gets big. Then it will pay for itself by allowing You to have simple and clean code instead of monolyth or dependency hell.

## Sample

Below are very basic sample of [Micon][micon] DI (this example doesn't show auto-discovering and  auto-configuring, it's too simple, for more complicated examples please see [Micon documentation][micon]).

``` ruby
require 'micon'
require 'logger'

# Registering `:logger` component.
micon.register(:logger){Logger.new STDOUT}

class Application
  # Whiring the `:logger` component and application together.
  inject logger: :logger

  # Now You can use `:logger` as if it's an usual class member.
  def run
    logger.info 'running ...'
  end
end

# Run it and You should see in console something like this:
#
#     [2011-08-16T19:09:05.921238 #24944]  INFO -- : running ...
#
Application.new.run
```

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

Tags : Complexity, DI, IoC
Date : 2010/5/1

[micon]: http://alexeypetrushin.github.com/micon
[icon]: https://firenet.s3.amazonaws.com/fs/4da14c38743f0f218d00000f/4da14ea9743f0f218d000015/4e095bc7743f0f489a000033/dark%20side.thumb.jpg