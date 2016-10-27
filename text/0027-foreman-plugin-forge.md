- Feature Name: A Forge for Foreman plugins
- Start Date: 2016-10-24
- RFC PR: (leave this empty)
- Issue: (leave this empty)

# Summary
[summary]: #summary

A Forge for Foreman acting as a central resource for its growing plugin
ecosystem.

# Motivation
[motivation]: #motivation

Foreman has an expanding plugin ecosystem that includes plugins for compute
resources, config management integrations and provisioning. However,
discoverability experience can be made better.

There's a page for [Plugins](https://theforeman.org/plugins) on the Foreman
website,
a
[list of plugins](http://projects.theforeman.org/projects/foreman/wiki/List_of_Plugins) on
the wiki, apart from various official/user Github pages for further details
about each plugin. Documentation for plugins are also available at multiple
places. For example, [Foreman remote execution](https://theforeman.org/plugins/foreman_remote_execution/0.3/index.html) plugin's
docs are a part of the official website, but many plugins maintain docs within
README.md file in the project root.

Keeping information up to date on each page is a manual task and could use some
automation.

A new Forge would address the following points:

* Showcasing Foreman plugin ecosystem and discover new plugins
* Offer helpful commands/code snippet for installing the plugin via source, gem,
  rpm, deb
* Display a short README à la Github's rendering of README.md
* Show plugin popularity/trustworthiness via download stats
* Plugin management (list/install/uninstall) via a command like
  foreman-rake/hammer - does it even make sense for us to do?

# Detailed design
[design]: #detailed-design

A Foreman Forge page for plugin would provide all the information about that
plugin at a glance.

```
+-----------------------------------------------------------------------------+
| Foreman Forge                                                               |
+------------------------------------------------+----------------------------+
|                                                |                            |
|  foreman_my_plugin                             | [Downloads] 1,337          |
|                                                | [Rating] ****              |
|  [summary] My plugin for Foreman adds support  +----------------------------+
|  for my functionality so that users can do foo.|                            |
+------------------------------------------------+ [gem install plugin]       |
|                                                |                            |
|  [README.md] This section automatically reads  | [yum install plugin]       |
|  contents of README.md and displays the docs.  |                            |
|  README.md is the place where plugin authors   | [apt-get install plugin]   |
|  are expected to write installation steps and  |                            |
|  other relevant documentation.                 | [github.com/user/plugin]   |
|                                                |                            |
+------------------------------------------------+----------------------------+
```

## Data sources

Foreman plugins are implemented as Ruby Gems. Therefore, a lot of metadata is
already available in the form of Gemspec attributes.

* foreman_plugin_name
* Plugin summary
* Author

Additionally, more metadata is available from RubyGems API
(example:
[foreman_remote_execution](https://rubygems.org/api/v1/gems/foreman_remote_execution.json))

* Downloads
* Downloads for current version
* Github URL
* Documentation URL

## Web application

Depending on how rich we want the Forge to be, one of the following approaches
may be taken.

### Jekyll based static Forge

We're already using Jekyll for building a static website with much success. A
Forge based on Jekyll would have:

* A master plugin list in JSON/YAML format. Plugins can be added/remove from the
  catalog by sending a PR against this file.

```
foreman_my_plugin:
  version: 1.2
  rubygem: <if it doesn't follow gem naming convention>
  gitrepo: https://github.com/myusername/foreman_my_plugin
  rpm: <if it doesn't follow rpm package naming convention>
  deb: <if it doesn't follow deb package naming convention>
```

* An external Jekyll script would read this JSON/YAML catalog and start
  gathering data points from RubyGems.org API. 
  
* Fill up templates with these data points and generate a unique static page for
  Foreman plugin.

### Rails based live Forge

A Rails app would essentially do the same as above, but also allow users to
create plugins manually. This way we can also extend it to have plugin ratings.

# Drawbacks
[drawbacks]: #drawbacks

The biggest drawback is - are we over-engineering it? Foreman plugins are
already distributed satisfactorily via yum/apt repos that we provide.

# Alternatives
[alternatives]: #alternatives

A far simpler way to implement a Forge would be by extending Jekyll based
theforeman.org website itself and add plugins like thus:
https://theforeman.org/forge/foreman_my_plugin and a forge index page. Additions
and updates to plugins can be made via PR
to [theforeman.org](https://github.com/theforeman/theforeman.org) project.

# Unresolved questions
[unresolved]: #unresolved-questions

* Do we want to address plugin discoverability, or distribution too?

* In case of a live Forge, do we duplicate user management? Does Foreman's
  Redmine instance support SSO so that all existing users are automatically
  Forge users too.
