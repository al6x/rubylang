# Micon - silent killer of dependencies and configs

[Micon](http://alexeypetrushin.github.com/micon) allows You easilly and transparently eliminate
dependencies and configs. Or, in other words, it allows You to do the followings:

- Split system into components that know nothing about each other except the name and a couple of
public methods.
- Completelly eliminate any notion of configs in code.
- Easily override behaviour of components in variety of assembly and environments.
- Completelly eliminate the *require 'xxx'* or any other notion of dependency in the form of
file or path.
- Automatically manage the life-cycle of components.
- Automatically build components for specs/tests and isolate it (restore component's state after
the spec/test).
- There's almost no price for all of it (except for needs to know 2 methods and the name of one
specific folder for component definitions).

Techincally [Micon](http://alexeypetrushin.github.com/micon) is sort of Dependency Injector, but
because of it's simplicity and invisibility it looks like an alien compared to it's complex and
bloated IoC / DI cousins.

Tags : Code, Complexity, DI, IoC
Date : 2011/10/1