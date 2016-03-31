# File Architecture 

## Introduction

The purpose of this document is to outline a mostly sensible folder and file architecture for front-end development of non-trivial applications.
The guiding principle of this architecture is to create hundreds (and possibly thousands) of small, single-purpose components.
The design goal of each component is that it is completely autonomous, and is agnostic of it's surrounding environment.
While components can be composed of other components, any component that is composed of two or more components should alert the developers intuition to a possible code smell.
Such components are better logically grouped as modules.

## Folder Structure

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

### Quick overview
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
	    * fonts: Folder containing fonts (if any)
	    * images: Folder for image assets
	* pages: Folder for full-blown pages (routed views)
	* app.styl: File used to import all application-specific stylesheets
	* vendor.styl: file used to import all third-party stylesheets
	  * note that The goal isn't to put ALL stylesheets in there, basically just the entry points and the generic parts (e.g., variables, mixins, ...)
	* index.html: The entry point of the single-page application
  * test: Folder Containing all end-to-end tests
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

## Directories

### Modules

In projects using ES6 and leveraging module exports, a module can be solely responsible for composing multiple logically
related components and providing a single interface for importing the composed module and destructuring it's individual exports.
For Aurelia, and other projects that favor convention over configuration, a module may simple compose multiple modules and not concern itself with exporting it's sub-components.

### Naming convention
 *  <module-name>: folder for the component
   *  <module-name>.module.js: model of the component
   *  <module-name>.module.spec.js: tests for the controller of the component
   *  <module-name>.component.module.js: service for the component
   *  <module-name>.component.module.spec.js: tests for the service of the component
   *  <component-name>.component.html: template/partial view of the component
   *  <component-name>.component.js: model of the component
   *  <component-name>.component.styl: styles for the component
   *  <component-name>.component.spec.js: tests for the controller of the component
   
#### Example
An example of an ES6 module with individual exports and composed exports:

```
import {AccountModel} from './account.model';
import {AccountService} from './account.service';
import {AccountList} from './account-list.component';
import {AccountDetail} from './account-detail.component';

export *  from './account.model';
export * from './account.service';
export * from './account-list.component';
export * from './account-detail.component';

export var ACCOUNT_DIRECTIVES: Array<any> = [
  AccountList,
  AccountDetail
];

export var ACCOUNT_PROVIDERS: Array<any> = [
  AccountModel,
  AccountService
];
```
   
### Components
The idea being to isolate components as much as possible so that removing one can be done in a clean manner without worrying (too much) about side-effects.
Think of components as custom elements that enclose specific semantics, styling, and behaviour.
 
*A component should solve one problem and solve it well.*
 
### Naming convention
 *  <component-name>: folder for the component
   *  <component-name>.component.html: template/partial view of the component
   *  <component-name>.component.js: model of the component
   *  <component-name>.component.styl: styles for the component
   *  <component-name>.component.spec.js: tests for the controller of the component
   *  <component-name>.component.service.js: service for the component
   *  <component-name>.component.service.spec.js: tests for the service of the component
 
If the component name is composed (which you should avoid if possible), then separate the parts with periods (.).
 
#### Additional guidelines
 * each component MUST be placed in its own directory
 respect the namespace rule above to correctly isolate/encapsulate the component
 * components CANNOT be nested (i.e., use a flat structure)
 
## Pages
This folder should contain all the routed *pages* or *views* of the application. The naming of `pages` can be substituted for `views` if desired and applicable.
In projects where views have another meaning such as for partials or full-page static views (i.e. 404, 500, etc) it is more applicable to use `pages` nomenclature. 

Pages can also be considered as *components* but they are an *aggregations* of other components and modules as well as specific HTML/CSS/JS.

### Naming convention
 *  <page-name>: folder for the page
   *  <page-name>.page.html: template/partial view of the page
   *  <page-name>.page.js: model of the page
   *  <page-name>.page.styl: styles for the page
   *  <page-name>.page.spec.js: tests for the controller of the page
   
## Core files
The core files of the application required for running the application.

#### src/app.js
This is the top element of the application. This should be loaded by src/main.js (see below).

```
export class App {
	...
	constructor(){
		console.log("Hello world!");
	}
}
```

#### src/main.js
The main.js file is the entry point of the application. 
This file is responsible for bootstrapping the application (instantiating the framework, registering the DOM, etc.).

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

## Build and Test Files and Folders

### Gulp
The gulp folder should contain two directories, config and tasks. 
The tasks directory should contain individual gulp tasks. 
The config directory should contain gulp plugin settings and a paths.js file with file and folder globbing patterns. 

Although we want to limit this list as much as possible, for everything to build successfully, the following files are needed:

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
For that configuration to work, the following devDependencies must also be added to the project:

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
  * common/*, pages/* allow you to import modules from different parts of the codebase without having to specify relative or absolute paths. This is covered in the folder structure section above.

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
