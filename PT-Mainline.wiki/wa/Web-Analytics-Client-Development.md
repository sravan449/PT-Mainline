This page will help you set up a dev environment for working on the Web Analytics client, i.e. the code that runs in a web browser.

**These instructions apply only to the 9.2.0.1 code.**

# Initial Setup

## Node.js
Node.js is the equivalent of a JVM for Javascript. It also contains a package manager 
to download, install and update dependencies. You need it in all cases.  
Download it and run the installer at:

~~~
https://nodejs.org/en/download
~~~

## Eclipse Projects

From the Git view, import the project
~~~
`server/com.ibm.rational.test.lt.client.analytics`.
~~~
This project contains resources to be compiled into a set of javascript, CSS and images/fonts.
**You must compile it before being able to use it in a browser**. You can use the command 
line or Eclipse.

## Eclipse Plug-Ins

Follow this section only if you want to develop directly using the Eclipse environment.

### Palantir Typescript

We use this plugin to edit and compile typescript code to javascript. This is only used for fast development. Production javascript is generated using the tools installed with npm above.

Save the typescript plugin to your local disk from [this link](tools/com.hcl.typescript.update253.zip).

Then in Eclipse, go to Help > Install Software...  
Click Add... then Archive... and select the file that you just downloaded.  
Select the only available check box (Typescript) and proceed through the next pages.

### Less

1. Node module

In a command line, type:
~~~
npm install -g less
~~~

2. Eclipse Plug-In

This plugin is used to edit and compile Less files to CSS files.
Click Help \> Install New Software... and in `Work with` paste this URL:
~~~
http://p2-dev.pdt-extensions.org/
~~~
Then select `Tool Chain` > `Transpiler Feature` and proceed to install.

_Note: This plug-in is also available on the Eclipse market place but the installation 
fails with this method._

3. Eclipse Preference

Go to Preferences... \> Transpilers.  
Click Add..., select LESS, and in executable, type the path to the LESS executable. On Windows, 
this is by default:
~~~
"C:\Program Files\nodejs\node" C:\Users\<user>\AppData\Roaming\npm\node_modules\less\bin\lessc
~~~

# Everyday tasks

## Command-line build

Open a command line and go to your Git working tree, at `server/com.ibm.rational.test.lt.client.analytics/web`.  
Type either of:

Production files: `npm run build`  
Development files: `npm run dev`  
The development files contains source mappings, so it is highly advised to use this 
version for debugging the application. Use the production version to test the files 
as they will be delivered in the product.

## Development with Command Line Build

Open a command line and go to your Git working tree, at `server/com.ibm.rational.test.lt.client.analytics/web`.   
Type:

`npm run watch`

Any change to a source file (.ts, .less or src/index.html) will trigger a rebuild of 
impacted resources.  
_Note: this seems to fail rebuilding .css files when a .less file is modified in the 
`src/stylesheets/base` directory. In that case, hit Ctrl+C then restart `npm run watch`._

## Eclipse build

If you followed the initial steps above, Eclipse will automatically build typescript 
code whenever a change is made to .ts files.

Less files can be compiled by touching the files in `src/stylesheets/default`. The corresponding 
.css file will generated into `web\themes\current`.

## Running the application

From the dev environment, run RPT.

It is highly advised to use an external browser rather than the integrated browser in Eclipse. To do so, in xPT/xFT go to `Preferences > Test > Performance Test Reports`, and select `In external browser`.

By default, RPT opens the application compiled by the command line.  
To open the application compiled by Eclipse, edit the URL and change `index.html` to 
`index2.html`.


# Maintenance tasks

## Node modules update

Open a command line and go to your Git working tree, at `server/com.ibm.rational.test.lt.client.analytics/web`.  
Type:

`npm update --dev`

This will update the node modules to their latest compatible version (i.e. it will not 
automatically upgrade modules to a new major versions).

Do not forget to commit the modules update.

## Adding a new module

If you need to add a new module, type

`npm install <module> --save` if the module is required at run time, or  
`npm install <module> --save-dev` if the module is required at build time.

This will update the files `package.json` and `package-lock.json`.

Do not forget to commit the modules update.

## Removing a module

If a module is no longer necessary, remove its entry from  `package.json`, then run

`npm prune`

Do not forget to commit the modules update.


# Project layout

~~~
src              Root of user-generated materials.
  stylesheets
    base         Individual stylesheets in LESS format
    default      Main stylesheets, define shared variables and include files from base. For each file
                 in this folder, a corresponding .css file is generated.
  ts             Root of the typescript source code
  index.html     HTML template of the generated html.index. This file does not contain javascript includes,
                 nor CSS includes (they are injected during the build).
node_modules     Node modules required to build and/or run the application.
web              Root of the files served by the HTTP server (under /analytics/web).
  images         Static images. Ultimately these files should be moved to src, once the mechanism
                 of webpack dependencies is fully implemented in .ts / .less files.
  jsdev          Result of .ts compilation by Eclipse. These files used AMD and require require.js.
                 They are used when opening index2.html
  libs           Libraries only required when running the index2.html. These are the libraries not managed
                 as node_modules (because they are not required to build the web resources).
  nl             Provides JSON localization resources for supported locales.
  test           Contains test code. Should be moved to src ultimately.
  themes         Contains the CSS resources required by the application. This folder should disappear
                 once the mechanism of webpack dependencies is fully implemented in .less files.
    celtic-banjo
    current      Result of the compilation of .less files in src/stylesheets/default
  res            Result of the compilation using command line. This folder must be part of the
                 installation. It currently contains all javascript (from libraries and our source)
                 plus CSS and resources from libraries. It will also contain CSS and resources from us
                 once the mechanism of webpack dependencies is fully implemented in .ts/.less files.


~~~