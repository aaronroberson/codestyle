### Folder structure & files

This folder structure is inspired by Google's [Best Practice Recommendations](https://docs.google.com/document/d/1XXMvReO8-Awi1EZXAXS4PzDzdNvV6pGcuaF4Q9821Es/pub).

        ├── doc/
        │  └── CHANGELOG.md
        ├── gulp/
        │  ├── tasks/
        │  │  └── *.js
        │  └── configs/
        │     └── *.js
        ├── jspm_components/
        ├── nodes_modules/
        ├── src/
        │   ├── assets/
        │   │  ├── fonts/
        │   │  └── images/
        │   ├──  common/
        │   │  ├── components/
        │   │  │   ├── navbar/
        │   │  │   │  ├── navbar.component.html
        │   │  │   │  ├── navbar.component.js
        │   │  │   │  ├── navbar.component.styl
        │   │  │   │  └── navbar.component.spec.js
        │   │  │   ├── modules/
        │   │  │   ├── layout/
        │   │  │   ├── services/
        │   │  │   │  └── contentschema/
        │   │  │   └── styles/
        │   │  │      ├── variables.styl
        │   │  │      └── mixins.component.spec.js
        │   ├── components/
        │   │  ├── navbar/
        │   │  │  ├── navbar.controller.js
        │   │  │  └── navbar.html
        │   ├──  modules/
        │   │  └── somemodule/
        │   │     └── somemodule.module.js
        │   ├──  pages/
        │   │  └── moments/
        │   │       ├── moments.page.html
        │   │       ├── moments.page.js
        │   │       └── moments.page.spec
        │   ├── 404.html
        │   ├── app.js
        │   ├── app.sty
        │   ├── favico.ico
        │   ├── index.html
        │   ├── main.js
        │   └── vendor.scss
        ├── test/
        │  └── e23/
        ├── .babelrc
        ├── .editorconfig
        ├── .eslintignore
        ├── .eslintrc
        ├── .gitignore
        ├── .npmignore
        ├── aurelia.protractor.js
        ├── aureliafile.js
        ├── env.js
        ├── gulpfile.babel.js
        ├── jsconfig.json
        ├── jspm.conf.js
        ├── karma.conf.js
        ├── LICENSE
        ├── package.json
        ├── protractor.conf.js
        └── READMME.md

* project root
  * doc: Folder containing all documentation and changelog
  * gulp: Folder containing all gulp tasks and configuration files
  * src: Folder containing all the source files of the application
	* components: Folder containing re-usable components (single concern)
	* common: Folder containing common reusable code (e.g., base utilities)
	  * components: Folder containing generic components (single concern)
	  * modules: Folder containing modules (logical collections composed of components)
	  * services: Folder containing generic services (e.g., for local storage)
	* assets: Folder containing all static assets (ideally offloaded to a CDN)  
	    * fonts: Folder containing fonts of your application (if any)
	    * images: Folder for image assets
	* pages: Folder for full-blown pages (routed views)
	* app.styl: file used to import all application-specific stylesheets
	* vendor.styl: file used to import all third-party stylesheets
	  * note that the goal isn't to put ALL your stylesheets in there, basically just the entry points and the generic parts (e.g., variables, mixins, ...)
	* index.html: the entry point of your application
  * test: Folder containing all end-to-end tests
  * .babelrc: Babel configuration file
  * .eslintrc: ESLint rule set to use while checking JavaScript code quality
	* reference: http://eslint.org/docs/user-guide/getting-started
	* note that the file is actually optional but indeed recommended!
  * .eshintignore: Files and folders to ignore while checking JavaScript code quality
  * aureliafile.js: Aurelia configuration for scripted builds
  * gulpfile.babel.js: gulp configuration file
  * jspm.conf.js: SystemJS/JSPM configuration file
  * karma.conf.js: Karma configuration file (configuration of the test runner)
  * package.json: NPM configuration file (also used by JSPM)
  * protractor.conf.js: Protractor e2e test configuration file

### Minimal (build-related) required file contents
Although we want to limit this list as much as possible, for everything to build successfully, some files need specific contents:

#### .babelrc
```
{
  "optional": [
    "es7.decorators",
    "es7.classProperties",
    "es7.asyncFunctions",
    "runtime"
  ]
}
```

With the configuration above, Babel will transpile ES2015 code to ES5 commonjs. Currently, Aurelia only supports Babel5. When support for Babel v6 is added, the .babelrc file may change to the following:
```
{
	"presets": ["es2015"],
	"plugins": ["transform-es2015-modules-commonjs"],
	"comments": false
}
```
For that configuration to work, the following devDependencies must also be added to your project:

```
"babel-plugin-transform-es2015-modules-commonjs": "^6.3.0",
"babel-preset-es2015": "^6.3.0",
```

#### jspm.conf.js
The SystemJS/JSPM configuration file plays a very important role;
* it is where all actual application dependencies are to be defined
* it is where you can define 'paths' for loading modules easily without having to specify relative paths (i.e., create aliases for paths).

Add dependencies to the project using `jspm install -y`; check the [official JSPM documentation](http://jspm.io/) to know more about how to install packages.

With the help of this configuration file, SystemJS will be able to load custom and third-party dependencies using ES2015 style (e.g., `import {x} from "y"`). In order for this to work, the SystemJS/JSPM configuration must be included in the main index.html.

Here's an example:
```
defaultJSExtensions: true,
  transpiler: "babel",
  babelOptions: {
    "optional": [
      "es7.decorators",
      "es7.classProperties",
      "es7.asyncFunctions",
      "runtime"
    ]
  },
  paths: {
    "*": "dist/*",
    "github:*": "jspm_packages/github/*",
    "npm:*": "jspm_packages/npm/*"
	"common/*": "./dist/common/*",
	"pages/*": "./dist/pages/*"
  }
});
```

In the above:
* defaultJSExtensions: is mandatory so that extensions don't have to be specified when importing modules
* transpiler: is set to true for in-browser transpilation (this is not optimal or suggested for production)
* paths
  * common/*, pages/* allow you to import modules from different parts of your codebase without having to specify relative or absolute paths. This is covered in the folder structure section above.

#### package.json
In addition to the dependencies listed previously, the following jspm configuration can be found in the package.json file:

```
  "jspm": {
	"directories": {},
	"configFile": "jspm.conf.js",
	"dependencies": {
	},
	"devDependencies: {
	}
  }
```

This is where you let JSPM know where to save the information about dependencies you install. This is also where you can easily add new dependencies; for example: `"angular2": "npm:angular2@^2.0.0-beta.1",`.
Once a dependency is added there, you can invoke `jspm install` to get the files and transitive dependencies installed and get an updated jspm.conf.js file.

#### karma.conf.js
Karma loads his configuration from karma.conf.js. That file contains everything that Karma needs to know to execute unit tests.

Because this project uses JSPM, it also uses karma-systemjs: https://www.npmjs.com/package/karma-systemjs

Configuration Reference: http://karma-runner.github.io/0.13/config/configuration-file.html

Dev dependencies:
```
	"jasmine": "...",
	"karma-jasmine": "...",
	"karma-jspm": "..."
```

#### src/app.js
This is the top element of the application. This should be loaded by src/main.js (see below).

```
"use strict";

export class App {
	...
	constructor(){
		console.log("Hello world!");
	}
}
```

#### src/main.js
The main.js file is the entry point of your application.

#### src/app.styl
The app.styl file is where all the stylesheets scattered around in the application get loaded.

Here's an example of a app.styl:
```
//
// Main stylesheet.
// Should import all the other stylesheets
//

// Variables, functions, mixins and utils
@import "common/styles/variables";
@import "common/styles/functions";
@import "common/styles/mixins";
@import "common/styles/utils";
```

In the example above, you can see that in the app.styl file, we import many other stylesheets (stylus partials).

#### src/vendor.styl
The vendor.styl file is the entry point for all vendor styles:
```
//
// Includes/imports all third-party stylesheets used throughout the application.
// Should be loaded before the application stylesheets
//

// Nicolas Gallagher's Normalize.css
@import '../jspm_packages/github/necolas/normalize.css@3.0.3/normalize.css'; // the path refers to the file at BUILD time
```

As you can see above, a third-party stylesheet is imported.
