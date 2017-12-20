# **mdlink**: Linking multiple node modules with a single command

# Introduction

As part of the process of working in **node.js** applications, developers usually use additional libraries and frameworks to ease and speed their development time, and more than often the number of libraries they rely upon is quite high.

It is also not uncommon to need to modify any of those libraries to fix a bug, or fork them to add additional functionality required for the project they are being used in.

The usual way to do this in a **node.js** project is:

  1. Clone the library's repository in your local machine
  2. `cd` your library's folder.
  3. `sudo npm link` to link the module globally.
  4. `cd` to your project's folder.
  5. `npm link <library_name>` to link to the library.


If you have multiple libraries that you need to be linking at the same time, or you have to link a library that depends on another library that you may need to link as well, it can be quite cumbersome to link them one by one.

In my free time I developed [mdlink](https://www.npmjs.com/package/mdlink). It is a small utility that allows to easily `npm link` multiple node modules in a given project.

It was born as a reaction of my annoyance while I was developing **Ember.js** applications with multiple addons at the same time. Per each addon I'd need to clone it and link it. This tool intends to provide an easy way of bulk `npm link` and removing those links later while not necessary.

Modules are specified in the **mdlink.config.json** configuration file. e.g:

```json
{
  "base_modules_path": "~/gits/test",
  "modules": {
    "mdlink": {
      "url": "https://github.com/fr0gs/mdlink",
      "path": "~/gits/test/mdlink"
    }
  }
}
```

Each module to be linked is stated in the *modules* object. It can have both *url* & *path* together, or only one of each.

The *base_modules_path* is used for the case where there is no *path* but a *url* is yet specified. The module will be cloned in `base_modules_path/module_name` instead.

Let's see an example with a new **Ember** application. First, download [mdlink](https://www.npmjs.com/package/mdlink) with **npm** and  create the application:

```sh
test-ember:master* λ sudo npm install -g mdlink
test-ember:master* λ ember new test-ember
```

Then, create a new *mdlink.config.json* file inside the `test-ember` app.

```sh
test-ember:master* λ mdlink init
```

And let's say that in our new application we want to locally modify both **ember-moment** and **ember-promise-helpers**. We modify the *mdlink.config.json* file like this:

```json
{
  "base_modules_path": "/home/esteban/gits/test",
  "modules": {
    "ember-moment": {
      "url": "https://github.com/stefanpenner/ember-moment",
      "path": "/home/esteban/gits/test/ember-moment"
    },
    "ember-promise-helpers": {
      "url": "https://github.com/fivetanley/ember-promise-helpers.git",
      "path": "/home/esteban/gits/test/ember-promise-helpers"
    }
  }
}
```

Do the linking:

```sh
test-ember:master* λ mdlink s
[+] Path /home/esteban/gits/test-ember/node_modules/ember-moment already exists, removing it.
[+] <path exists> . Successfully cloned https://github.com/stefanpenner/ember-moment in path: /home/esteban/gits/test/ember-moment
[+] Create link from /usr/lib/node_modules/ember-moment -> /home/esteban/gits/test/ember-moment
-------------------------
[+] <path exists> . Successfully cloned https://github.com/fivetanley/ember-promise-helpers.git in path: /home/esteban/gits/test/ember-promise-helpers
[+] Create link from /usr/lib/node_modules/ember-promise-helpers -> /home/esteban/gits/test/ember-promise-helpers
```

Verify both modules where properly cloned:

```sh
test λ ls -la ~/gits/test/
drwxrwxr-x  4 esteban esteban 4,0K Dez 19 22:07 ./
drwxrwxr-x  8 esteban esteban 4,0K Dez 19 22:05 ../
drwxrwxr-x 10 esteban esteban 4,0K Dez 19 22:36 ember-moment/
drwxrwxr-x  9 esteban esteban 4,0K Dez 19 22:07 ember-promise-helpers/
```

Modify *index.js* in both modules, and `ember s` from the `test-ember` app folder:

```sh
test-ember:master* λ ember s
Linked ember-moment
Linked ember-promise-helpers
Could not start watchman
Visit https://ember-cli.com/user-guide/#watchman for more info.
Livereload server on http://localhost:7020

Build successful (14117ms) – Serving on http://localhost:4200/
```
