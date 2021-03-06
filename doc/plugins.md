# Writing a plugin for the Amahi Platform

An Amahi platform plugin is an RoR engine. For simplicity we call them plugins.

Plugins should be located in `plugins/` and can be generated with a generator

```bash
rails generate plugin FooBar
```

This will generate a plugin called FooBar in the plugin/ directory of the app. The name of the directory is the class name for the plugin and it should be CamelCased for best results.

## Requirements

Each plugin has to have a file called `config/amahi_plugin.yml` with details of the plugin, for example:

	# human readable name (no localization supported yet)
	# for example, it may be used as the text of the tab or the page title, etc.
	name: Foo Bar Tab
	# class to be mounted
	class: FooBar
	# kind of plugin (so far we only support 'tab' plugins)
	kind: tab
	# url where it will be mounted in the platform
	url: /tab/foobar

No two apps have may use the same class or url to be mounted.

## Adding Tabs and Subtabs

To add visible tabs in a plugin, one has to do it programmatically at plugin initialization time with what in RoR is known as an initializer.

Example for initialization for a plugin (e.g. plugin/foo_bar/config/initializers/plugin_init.rb )

```ruby
# plugin initialization -- set up a tab by calling Tab.new:
# - first argument to Tab.new is the controller that it will hooked up to
# - second argument is a string, the label for the tab. This will support internationalization in the future
# - third argument is the route it should be mounted on, example /tab/foobar
t = Tab.new("foobar", "FooBar", "/tab/apps")
# add any subtabs to this tab with what you need.
# The params are
# - controller
# - the label
# for example
t.add("index", "All Foo Bars")
t.add("advanced", "Advanced Settings for Foos")
# this subtab has a third parameter -- denoting it's an advanced subtab
t.add("expert", "Expert Settings", true)
t.add("other", "Other Settings")
```

## Principles

* One folder = One plugin. This is a must. To install an Amahi plugin manually, all there needs to be done is copy a single folder into the plugins/ directory and done. It's good for manual installation, it's good for automatic installation.
* Plugins are activated automatically. We only need to restart the server (manual if in development), or programmatically if installing in production by touching tmp/restart.txt. No need to go into the DB ahave have to set anything. Any settings needed should be auto-generated.
* We will tend towards adding hooks to various areas to be able to override nicely in plugins.
* Try to not override views, so that behavior changing in the main app will not break plugins.

## To Do

1) Localization support for the labels and the sublabels

2) Support for subtabs (and tabs?) supported only when advanced settings are enabled

