# Common Interface

Common Interface is a tool for **Rapid Web Interface Creation** (more exactly, it's the concrete implementation of the [Abstract Inteface][abstract-interface] plugin).

Instead of trying to provide one universal user interface API (like GWT) to fit all needs, the [Abstract Interface][abstract-interface] **provides you with tools that ease creation of your own custom interface**.

Define Your design only one time, in one place, and then reuse it, forget about HTML and CSS in your Views.

## Overview

!![overview] It acts like an abstraction layer, allowing you to define your custom API (or DSL if you like) to build your user interface. Key point here - it allows you build such API **very quick and easy**.

## Real-life usage:

This site is 100% built by Common Interface, using the 'Simple Organization' theme (you can also see this page with the 'default' theme, [click here][default-theme]).
Actually it mixes two themes, because the cool 'Simple Organization' theme isn't completed, and many non-critical areas are built using the simple 'Default' theme.

[TODO in new version of system there's no folders anymore, fix this sentence]
(This [folder containing images for this page][folder] is built using mixture of two themes. It's not exactly obvious by just seeing it (because the 'Default' theme is also clean and white) but it is so.)

[http://4ire.net](http://4ire.net) itself (using the 'Default' theme) and all other sites listed on it's first page are 100% built by Common Inteface.

*By 100% I mean that there's no any custom HTML, CSS or hack, absolutely all pages built using Common Interface API.*

## Demos

- [Sample of HomePage][sample1], [sample of page (with another design)][sample2]
- [List of all Samples][list_of_samples]

## Features
<div class='right'>  !![features] </div>

* **Themes support** not only CSS, you can use completelly different HTML and layouts
* **DRY**
* **Clean views**
* **Iterative development**
* **Loose coupling of Logic and Design**
* **Share the same design with many Apps**
* **Mix two complete different themes** simultaneously, in the same page
* Start with simple prototype and when App matures create professional design (with minimum changes in App)
* Theme/Skin support (not only CSS but also Templates, Images, ...)
* Outsource design without opening App code
* Designers can go ahead and create working html/css/js
* Programmers can go ahead and create working App with simple design
* Both of them can do iterative prototyping that will be updated later

## Code Samples

Please see http://github.com/alexeypetrushin/rad_common_interface

## Usage

Please see the [readme][common-interface] and spec.
Source Code: http://github.com/alexeypetrushin/rad_common_interface
It is under heavy development so be ready that there may be bugs somewhere, if you are novice in Rails don't use it.

[overview]: https://firenet.s3.amazonaws.com/fs/4da14c38743f0f218d00000f/4da14ea9743f0f218d000015/4e2d4477743f0f1c7400005a/overview.thumb.png
[features]: https://firenet.s3.amazonaws.com/fs/4da14c38743f0f218d00000f/4da14ea9743f0f218d000015/4e2d4477743f0f1c7400005a/features.thumb.png
[folder]: /folders/folder-for-common-interface-zff
[default-theme]: /default/common-interface?_theme=default

[common-interface]: http://github.com/alexeypetrushin/rad_common_interface
[abstract-interface]: http://github.com/alexeypetrushin/rad_abstract_interface

[sample1]: http://4ire.net/ci_sites/home?layout_template=home&theme=simple_organization
[sample2]: http://4ire.net/ci_objects/page
[list_of_samples]: http://4ire.net/ci_demo

Tags : Design, UI
Date : 2010/6/1