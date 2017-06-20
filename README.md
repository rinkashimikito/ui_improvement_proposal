# Proposed modernisation of SPTs UI
The long term target for this update is to make code reuasble, easier to maintain and extend, using latest tools (or as latest as possible). Some of the proposed changes are not crutial but good to have. Other ones are more expensive but have many pros in a longer run. There is a summary table sorted by priority at the end of this document.
## The build process
There are few things we could do to improve our build process. I'm not saying that the process is bad and we definitely need to change it but some tools we use are not developed anymore and future extending might become tricky.
### Use Webpack (https://webpack.js.org/) for building front end
* grunt is a very old task runner and has not been updated for a year now
* webpack can handle (using plugins and modules) concatenating and minifying files, manage dependencies, run tests
* replacing grunt with webpack **is not crucial** as we can configure grunt to do what we need
* other option is to use Gulp which is faster than Grunt (Grunt is based on configuring separate independent tasks, each task opens/handles/closes file. Gulp requires less amount of code and is based on Node streams, which allows it to build pipe chains (w/o reopening the same file) and makes it faster)
### Add ENV (environment) based build
* minify JS/CSS on prod (smaller file for user to download)
* don't minify JS/CSS on dev (easier to maintain and develop)
* add JS/CSS sourcemaps (allows browser debugging)
* use CDN on production for files that are required by the website
### Use babel (https://babeljs.io)
* we could start using ES6 going forwards (Babel is a essentially an ECMAScript 6 to ECMAScript 5 compiler. It allows to use ES6 features in your projects and then compiles ES5 to use in production)
* we could gradually rewrite existing code to ES6

### Use linting (static code analysis) JS tool for testing against coding rules
In sum this helps devs to write code using same syntax and checks if there are errors in the code. https://github.com/airbnb/javascript , https://github.com/airbnb/css
* ESLint (http://eslint.org)
  * PROS
    * Flexible: any rule can be toggled, and many rules have extra settings that can be tweaked
    * Very extensible and has many plugins available
    * Easy to understand output
    * Includes many rules not available in other linters, making ESLint more useful for detecting problems
    * Best ES6 support, and also the only tool to support JSX
    * Supports custom reporters
  * CONS
    * Some configuration required
    * Slow, but not a hindrance
* JSHint (http://jshint.com)
  * PROS
    * Easier to setup than ESLint
    * Most settings can be configured
    * Supports a configuration file, making it easier to use in larger projects
    * Has support for many libraries out of the box, like jQuery, QUnit, NodeJS, Mocha, etc.
    * Basic ES6 support
  * CONS
    * Difficult to know which rule is causing an error
    * Has two types of option: enforcing and relaxing (which can be used to make JSHint stricter, or to suppress its warnings). This can make configuration slightly confusing
    * No custom rule support
* use SASS linting tool like https://www.npmjs.com/package/sass-lint
## Optimisation
This is what we could do to improve code readability, make HTML and CSS less tightly coupled, smaller in size and independent from 3rd party libraries.
### SASS
* cleanup sass structure so it's not coupled with the views
  * use BEM (http://getbem.com/introduction/)
* remove/avoid !important overrides; _if there is a need for !important then there must be something wrong_
* remove any redundant code
### JS
* remove jQuery dependency
  * do we really need jQuery? (http://youmightnotneedjquery.com/)
  * use Babel or shims/polyfills if needed
  * if we don't want to remove jQuery we could at least write an adapter for this library so in the future, if situation changes, we could just swap the library and update the adapter leaving the code untouched
* if we can't live without jQuery we could at least replace it with micro library (e.g. https://github.com/kylebarrow/chibi)
* _if we use webpack/babel or remove jQuery then we will have to refactor all JS modules so it's managed by it; this could be done in one go but it doesn't have to be_
#### Review tests
In order to refactor JS code we will have to have full tests coverage therefore the existing tests should be reviewed.
### View rendering
In order to decrease code duplication and making the website composable (builded from separate components) and easier to maintain/extend we could create composable components from existing code so we can share them across the website(s). We could do it by:
* using partials
  * binded with Model
  * _probably not possible to share across gov.uk websites_
* using view components
  * using properties passed to component instead of a Model
  * _probably not an option for us (speak with backend dev)_

If there is no way we could refactor views so they are composable (shareable) then probably the way to go would be going with the new front end framework (this should be discussed with backend dev)
## Front end framework
One of the way to modernise UI is to use modern front end framework. The website is quite simple and there is not much of user interaction involved so this **might not be** necessary. Examples:
* **https://reactjs.net/getting-started/download.html (.NET integration for ReactJS)**
* https://blogs.msdn.microsoft.com/webdev/2017/02/14/building-single-page-applications-on-asp-net-core-with-javascriptservices
* https://github.com/lijunle/Nancy.ViewEngines.React (Use React.js as view engine in Nancy)
* https://github.com/pauldotknopf/react-aspnet-boilerplate

There are some reasons why we could go with one of above but there are also some caveats.
* PROS:
  * We can render the React components on the server side (good for non-js devices, good for SEO)
  * React components are composable and data driven which means they respond to properties/parameters/data passed and re-render themselves when the pros are changing
  * Easy to follow data flow (from parent components to their children) and html structure
  * the website is simple enough to use React by unexperienced devs
* CONS :
  * replacing View Engine and all Razor views will take quite a lot of time
  * devs have to be comfortable with React (but see the last pro)
  * there are ways of UI testing react components but it's not straightforward

### Testing utilities
Updating front end framework will require testing utilities. There is a plenty of them but for React the best would be probably Jest (https://facebook.github.io/jest) and/or Enzyme (http://airbnb.io/enzyme). The decision about test framework should be preceded by a spike story.

### Next steps
As the next step is to choose what is the best for the business have a look at below table which have all above options prioritised and divided into three suggested sets.

The **basic** package has most important updates. Need to be done.

The **extended** package includes the basic one + good to have updates.

The **full** package includes all of required and suggested options. It is most expensive (time) but eventualy the code will be greatly maintainable and extendable. The code will be composable and reusable across all of the gov.uk websites (if other pages use the same architecture). There is a drawback with the new front end framework, or in this case view library (React), which will have to be adopted by other devs who may not know it (yet ;)).

Basic | Extended | Full
------|----------|-----
ENV based build | ENV based build | ENV based build
SASS optimisation | SASS optimisation | SASS optimisation
Tests review | Tests review | Tests review
JS optimisation (add library adapter) | JS optimisation (adapter + replace jQuery with micro library) | JS optimisation (remove jQuery)
|| Babel (ES6) | Babel (ES6)
|| View rendering refactor | View rendering refactor
|| JSHint | ESLint
|| SASSLint | SASSLint
|| Gulp | Webpack
||| Front end framework
||| Updated testing frameworks
