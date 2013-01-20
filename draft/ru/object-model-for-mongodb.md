# Обьектная Модель для MongoDB

[Обьектная Модель][mongodb_model] для MongoDB.

Особенности:

- Модели - обычные (почти) Руби-Обьекты.
- Одинаковый API для [Драйвера][driver] и [Модели][mongodb_model].
- Нет лишних абстракций, все максимально близко к концепциям MongoDB.
- Нет схемы (можно определить если нужно, а также опции mass-assignment).
- Модели могут быть сохранены в любую колекцию, динамически.
- Полная поддержка композитных / встроенных моделей (с валидациями, коллбеками, ...).
- Скоупы, scopes, default scopes.
- Не пытается иммитировать ActiveRecord, MongoDB - другая и этот инструмент сделан чтобы по максимуму использовать ее особенности.
- Работа с несколькими базами и коллекциями одновременно.
- Поддержка ассоциаций.
- маленький размер, [статистика кода][code_stats].

Это инструмент **явно использует сильные стороны MongoDB и учитывает ее особенности**.

Инсталляция:

``` bash
gem install mongodb_model
```

Для интеграции с Rails:

``` ruby
require 'mongo/model/integration/rails'
```

Пример кода:

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

[driver]:       http://alexeypetrushin.github.com/mongodb
[code_stats]:   http://alexeypetrushin.github.com/mongodb_model/code_stats.html
[mongodb_model]: http://alexeypetrushin.github.com/mongodb_model

Tags : MongoDB