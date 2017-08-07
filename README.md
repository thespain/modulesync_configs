ModuleSync Configs
==================

This repository contains default configuration for
[modulesync](https://github.com/voxpupuli/modulesync) for the modules
I maintain.


Configuring ModuleSync
----------------------

**`modulesync.yml`**

A key-value store of arguments to pass to ModuleSync. Each key is the name of a
flag argument to the msync command. For example, `namespace: myusername`
represents passing `--namespace myusername` to msync. This file does not appear
in this repository because it only serves to override default configuration.

**`managed_modules.yml`**

A YAML array containing the names of the modules to manage.

Defining Module Files
---------------------

**`config_defaults.yml`**

Each first-level key in this file is the name of a file in a module to manage.
These files only appear here if there are templates in the moduleroot/
directory that need to be rendered with some default values that might be
overridden. The files listed do not necessarily represent all the files that
will be managed. The files in moduleroot/ represent all the files that will be
managed, except for unmanaged and deleted files (see [#Special Options]).

**`.sync.yml`**

This file should appear in the module itself if there are any values to
override from the config_defaults.yml file or if there are any additional
values to assign. A description of what optional values can be defined in
.sync.yml follows in the description of each file in moduleroot/. .sync.yml
will have the same format as config_defaults.yml.

#### Note

Each template is rendered in slightly different ways. Your templates to not
need to be identical to these, as long as your config_defaults.yml or .sync.yml
files contain as first-level keys the exact names of the files you are
managing and appropriately handle the data structures you use in your templates
(arrays versus hashes versus single values).


Special Options
---------------

### Unmanaged Files

A file can be marked "unmanaged" in .sync.yml, in which case modulesync will
not try to modify it. This is useful if, for example, the module has special
Rake tasks in the Rakefile which is difficult to manage through a template.

To mark a file "unmanaged", list it in .sync.yml with the value `unmanaged:
true`. For example,

```
---
spec/spec_helper.rb:
  unmanaged: true
```

### Deleted Files

Managing files may mean removing files. You can ensure a file is absent by
marking it "delete". This is useful for purging nodesets.

To mark a file deleted, list it in .sync.yml with the value `delete: true`. For
example,

```
---
spec/acceptance/nodesets/sles-11sp1-x64.yml
  delete: true
```

# Adding a new module

To add a new module, append it to the `managed_modules.yml` file and create a PR
for the change here. To avoid any surprises, your module should already be
up-to-date with the current templates. To do so, run
`[bundle exec] msync --noop -f <module name>` and commit and push the changes in
`modules/<module name>` to your module. If you need to make local changes to
this, add a `.sync.yml` as described above and rerun `msync` with `--offline`
added. This will avoid blowing away the current checked out state. You can
repeat this process as often as you need to get the module in an acceptable
state.
