# Class Loader finds and loads classes for Your App

There's only one method - **autoload_dir**, kind of turbocharged autoload, it understands namespaces, dependencies and can watch and reload changed files.

Let's say your application has the following structure:

```
/app
  /animals
    /dog.rb  =>  class Dog; end
  /zoo.rb    =>  class Zoo; end
```

Just point ClassLoader to the directory(ies) your classes are located and it will find and load them automatically:

``` ruby
require 'class_loader'
autoload_dir 'app'

Zoo.new           # both classes
Animals::Dog.new  # are loaded automatically
```

no need for:

``` ruby
# require 'animals/dog'
# require 'app'
```

You can specify multiple autoload directories, and tell it to **watch** them:

``` ruby
autoload_dir 'lib', true # provide true to watch & reload
autoload_dir 'another_lib'
```

**Note**: in the dog.rb instead of:

``` ruby
module Animals
  class Dog
  end
end
```

we write just the:
``` ruby
class Dog
end
```

There are no really the 'Animals' module, ClassLoader is smart enough to figure it out that there's should be one by looking at files structure and it will generate it on the fly.

It understands both **underscored** and **CamelCase** paths. Also You can provide Your own custom path translator and resource adapter (see code and specs for samples) to download classes from internet or database for example.

## Demo

This site uses it, so You are seeing it working right now ;).

## Installation

``` bash
gem install class_loader
```

Source: http://github.com/alexeypetrushin/class_loader

**Note:** It requires Ruby 1.9.x (there are actually no any special 1.9 code, but I don't use 1.8, please do it by yourself if You need it, the code is small and it should be easy).

## Problems

There is a known bug in Ruby 1.8.x - class loading isn't thread safe, so in production classes are usually preloaded when application started.
ClassLoader works with Ruby 1.9.x and I'm not sure is this bug fixed or not, but just in case, You can preload all classes by this command:

``` ruby
ClassLoader.preload! if app_in_production?
```

Tags : Code, Complexity
Date : 2010/5/1