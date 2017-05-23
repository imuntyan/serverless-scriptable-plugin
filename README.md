[![npm version](https://badge.fury.io/js/serverless-scriptable-plugin.svg)](https://badge.fury.io/js/serverless-scriptable-plugin)
[![Build Status](https://travis-ci.org/weixu365/serverless-scriptable-plugin.svg?branch=master)](https://travis-ci.org/weixu365/serverless-scriptable-plugin)
[![Test Coverage](https://codeclimate.com/github/weixu365/serverless-scriptable-plugin/badges/coverage.svg)](https://codeclimate.com/github/weixu365/serverless-scriptable-plugin/coverage)
[![Code Climate](https://codeclimate.com/github/weixu365/serverless-scriptable-plugin/badges/gpa.svg)](https://codeclimate.com/github/weixu365/serverless-scriptable-plugin)
[![Issue Count](https://codeclimate.com/github/weixu365/serverless-scriptable-plugin/badges/issue_count.svg)](https://codeclimate.com/github/weixu365/serverless-scriptable-plugin)


What's the plugins for?
------------------------
This plugin add script support to Serverless 1.0 which enables you to customize Serverless behavior without writing a plugin. 

It allows you to run nodejs script in any build stage.


Quick Start
-------------
1. Install

        npm install --save-dev serverless-scriptable-plugin
        
2. Add to Serverless config 

        plugins:
          - serverless-scriptable-plugin
    
        custom:
          scriptHooks:
            before:deploy:createDeploymentArtifacts: build/package.js

   Example File Structure
       
          Project Root 
              |___serverless.yml
              |___serverless.env.yml
              |___build
                  |___package.js
                  

Example
---------
1. Customized package behavior

   Currently, Serverless 1.0 package everything under service folder without any extra process. 
   There are serveral problems:
   
   - Not easy for transcompiling, e.g. using Babel/Typescript to transcompile code 
   - The default package is big because it packaged dev dependencies

   In babel-then-package-to-zip example, serverless will run customized package process: transcompile and package to zip
   
    ```js
    exec("Clean Up Environment", "rm -rf ._target lib *.zip &amp;&amp; mkdir -p lib");
    exec("Compiling", "node_modules/.bin/babel --presets es2015,react --plugins transform-async-to-generator,transform-runtime,transform-class-properties,transform-flow-strip-types -d lib/ src/");
       
    const packageBuilder = new PackageBuilder(servicePath);
    packageBuilder.addFolder("lib");
    
    //I only tested npm 3, not sure if npm 2 works or not.
    packageBuilder.addDependenciesExclude(["node_modules/aws-sdk"]);
       
    packageBuilder.writeToFileSync(artifactFilePath);
    ```

2. Run any command as a hook script

   It's possible to run any command as the hook script, e.g. use the following command to zip the required folders
 
        plugins:
          - serverless-scriptable-plugin
    
        custom:
          scriptHooks:
            after:package:createDeploymentArtifacts: zip -q -r .serverless/package.zip src node_modules
        
        service: service-name
        package:
          artifact: .serverless/package.zip

Change Log
-------------
- Version 0.5.0
  - [Feature] Supported serverless variables in script/command
  - [Improvement] Integrated with codeclimate for code analysis and test coverage
- Version 0.4.0
  - [Feature] Supported colored output in script/command
  - [Improvement] Integrated with travis for CI
- Version 0.3.0
  - [Feature] Supported to execute any command for serverless event
- Version 0.2.0
  - [Feature] Supported to execute javascript file for serverless event
