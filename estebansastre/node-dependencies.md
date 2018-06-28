# Types of dependencies in NPM.

## Introduction

When we start a new javascript project and we count on adding a minimal level of complexity to it, we will eventually need to make use of external libraries if we want to avoid writing every feature totally from scratch. For that matter, you can choose to take three approaches:
  * Download the library code bundle and adapt it to your development and deployment process. This is easy if the library is very small, you don't care about updating it's version, and you know very specifically what you want from it.
  * Use a [Content Delivery Network](https://en.wikipedia.org/wiki/Content_delivery_network) to access a remote copy of the library hosted in the cloud.
  * Use a tool like [NPM](https://www.npmjs.com/), [Bower](https://bower.io/), or the new kid in town [Yarn](https://yarnpkg.com/en/). This are package managers: pieces of software that allow the developer manage the projet's dependencies by fetching them from a central repository, specify each library's desired version and execute scripts for different steps of the development process.


When you are working on a project and decide to use npm as it's package manager, it will use a file named *package.json* in the root of the project's directory. This file contains the information about the dependencies on external libraries it has, along with other useful information. It sits as the point of reference that npm will use when fetching and updating your dependencies. More information about the package.json file [here](link to package.json explanation).

## How does it work.

Very briefly explained, npm will read the contents of your package.json file and will enumerate the libraries your current project depends on. It will then fetch each library in a recursive manner, namely, each one of those libraries may contain themselves a *package.json* file enumerating the dependencies that librarie relies upon, so it'll fetch them again, and so on, and so on.

Dependencies are expressed in a hash with a pair of

```
{
  "package_name_1": "version range",
  "package_name_2": "version range",
  ...
}
```

per each of them. THe version range can be a **string** indicating the version range to fetch from the central repository, a **tarball** or a **git url** pointing to a specific tag or commit.

There are different ways inside the *package.json* file to indicate the *nature* of the dependencies: **dependencies**, **devDependencies** and **peerDependencies**.
There also exists **bundledDependencies** and **optionalDependencies**, but I do not see them used that often, perhaps because the goal they pursue can be achieved with other tools.

# dependencies

These are the libraries your project directly depends on in order to properly execute. There is a direct dependency from the correct functioning of your application to the fetching of this packages. After compiling and minifying your javascript, the final bundle must include these libraries code to work.

# devDependencies

They represent the packages needed during the development of your project, such as tools and frameworks, that are not needed in the final product. An example for a project developed with the [Ember](https://www.emberjs.com/) javascript framework, could be:

```
"devDependencies": {
  "ember-cli": "2.11.1",
  "ember-cli-sass": "^5.3.1",
}
```

  * **ember-cli** is the command line tool that helps the creation of blueprints for the project, run scripts, etc.. much as the [rails](http://rubyonrails.org/) cli tool.
  * **ember-cli-sass** contains files to help translate the *sass* code into css code.

As you can see, a final user of your product is not interested to have this auxiliary tools in the final bundle.


# peerDependencies

**peerDependencies** are used to avoid name clashes and application breaks when an package depends on a library's version that is not the same as the specified one in the project's package.json. For example in my project's *package.json*:

```
{
  "name": "test_peer",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC",
  "dependencies": {
    "grunt-webpack": "^3.0.0",
    "webpack": "^1.0.0"
  }
}
```

I specify version "1" of webpack and also install **grunt-webpack**. **grunt-webpack** states a different webpack version in it's **peerDependencies** hash:

```
"peerDependencies": {
  "webpack": "^2.1.0-beta || ^2.2.0-rc || 2 || 3"
},
```

The result of `npm install` this project is:

```
test_peer@1.0.0 /home/esteban/gits/test/test_peer
├─┬ grunt-webpack@3.0.0
│ ├─┬ deep-for-each@1.0.6
│ │ └─┬ is-plain-object@2.0.3
│ │   └── isobject@3.0.0
│ └── lodash@4.17.4
└─┬ UNMET PEER DEPENDENCY webpack@1.0.0

... etc ...
```

This means that while in my project I downloaded version **1** of webpack, **grunt-webpack** depends on another set of versions to safely execute, so the package manager will intercede for us and warn us that this setup may break the program.


# bundledDependencies

They can also be written as *bundleDependencies*. They enumerate the dependencies that must be included in your project just as regular dependencies. They will also be included in the tarball generated as the result of executing `npm pack`.

They are used for example if you want to include third party libraries that are not part of the npm registry, or distribute your project as a module.

# optionalDependencies

Just like regular dependencies, but builds don't fail if the dependencies are not met.


References:

- [https://stackoverflow.com/questions/11207638/advantages-of-bundleddependencies-over-normal-dependencies-in-npm](https://stackoverflow.com/questions/11207638/advantages-of-bundleddependencies-over-normal-dependencies-in-npm)
- [https://www.npmjs.com/](https://www.npmjs.com/)
- [https://docs.npmjs.com/files/package.json](https://docs.npmjs.com/files/package.json)
- [https://stackoverflow.com/questions/18875674/whats-the-difference-between-dependencies-devdependencies-and-peerdependencies](https://stackoverflow.com/questions/18875674/whats-the-difference-between-dependencies-devdependencies-and-peerdependencies)
- [https://stackoverflow.com/questions/26737819/why-use-peer-dependencies-in-npm-for-plugins](https://stackoverflow.com/questions/26737819/why-use-peer-dependencies-in-npm-for-plugins)
