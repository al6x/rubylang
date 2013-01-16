# Object Model for MongoDB.

Features:

- Models are (almost) pure Ruby Objects.
- Same API for [Driver](http://alexeypetrushin.github.com/mongodb) and
[Object Model](http://alexeypetrushin.github.com/mongodb_model).
- Minimum extra abstractions, trying to keep things as close to the MongoDB semantic as possible.
- Schema-less, dynamic (with ability to specify types for mass-assignment).
- Models can be saved to any collection, dynamically.
- Full support for composite / embedded objects (with validations, callbacks, ...).
- Scopes, default scopes.
- Doesn't try to mimic ActiveRecord, MongoDB is differrent and the Object Model designed to get most of it.
- Works with multiple connections and databases.
- Support for associations.
- Small codebase, see [code stats](http://alexeypetrushin.github.com/mongodb_model/code_stats.html)

This tool **exposes simplicity and power of MongoDB and leverages its differences**.

Install MongoDB Model with Rubygems:

``` bash
gem install mongodb_model
```

For Rails integration use:

``` ruby
require 'mongo/model/integration/rails'
```

Code sample:

``` ruby
# In this example we'll create simple model and examine basic CRUD and
# querying operations.
require 'mongo/model'

# Connecting to test database and cleaning it before starting.
Mongo::Model.default_database_name = :default_test
Mongo::Model.default_database.clear

# Let's define Game Unit.
# Models are just plain Ruby Objects, there's no any Attribute Scheme,
# Types, Proxies, or other complex stuff, just use standard Ruby practices.
class Unit
  # Inheriting our Unit Class from Mongo::Model (the `inherit` keyword is
  # just a simple shortcut including Module and its ClassMethods).
  inherit Mongo::Model

  # You can specify collection name explicitly or omit it and it will be
  # guessed from the class name.
  collection :units

  # There's no need to define attributes, just use plain old Ruby technics to
  # of working with objects.
  attr_accessor :name, :status, :stats

  def inspect; name end
end

# Stats conaining statistics about Unit (it will be embedded into the
# Unit).
#
# There are no difference between main and embedded objects, all of them
# are just standard Ruby objects.
class Unit::Stats
  inherit Mongo::Model

  attr_accessor :attack, :life, :shield
end

# Let's create two great Heroes.
zeratul  = Unit.new name: 'Zeratul',  status: 'alive'
zeratul.stats =  Unit::Stats.new attack: 85, life: 300, shield: 100

tassadar = Unit.new name: 'Tassadar', status: 'dead'
tassadar.stats = Unit::Stats.new attack: 0,  life: 80,  shield: 300

# Saving units to database
p zeratul.save                                    # => true
p tassadar.save                                   # => true

# We made error - mistakenly set Tassadar's attack as zero, let's fix it.
tassadar.stats.attack = 20
p tassadar.save                                   # => true

# Querying, use standard MongoDB query.
p Unit.first(name: 'Zeratul')                     # => Zeratul
p Unit.all(name: 'Zeratul')                       # => [Zeratul]
Unit.all name: 'Zeratul' do |unit|
  p unit                                          # => Zeratul
end

# Simple dynamic finders (bang versions also availiable).
p Unit.by_name('Zeratul')                         # => Zeratul
p Unit.first_by_name('Zeratul')                   # => Zeratul
p Unit.all_by_name('Zeratul')                     # => [Zeratul]
```

Tags : MongoDB, Open Source
Date : 2011/12/1