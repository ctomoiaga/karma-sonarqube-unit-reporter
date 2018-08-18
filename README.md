# karma-sonarqube-unit-reporter


[![NpmLicense](https://img.shields.io/npm/l/karma-sonarqube-unit-reporter.svg)](https://github.com/tornaia/karma-sonarqube-unit-reporter) [![npm](https://img.shields.io/npm/dt/karma-sonarqube-unit-reporter.svg)](https://github.com/tornaia/karma-sonarqube-unit-reporter) [![NpmVersion](https://img.shields.io/npm/v/karma-sonarqube-unit-reporter.svg)](https://github.com/tornaia/karma-sonarqube-unit-reporter)

This solution is based on https://github.com/karma-runner/karma-junit-reporter .

Issue: https://github.com/karma-runner/karma-junit-reporter/issues/81

Available on npmjs.org
https://www.npmjs.com/package/karma-sonarqube-unit-reporter

Sample karma.conf.ci.js
```xml
'use strict';

var path = require('path');
var conf = require('./gulp/conf');

var _ = require('lodash');
var wiredep = require('wiredep');

var pathSrcHtml = [
  path.join(conf.paths.src, '/**/*.html'),
  path.join(conf.paths.src_test, '/**/*.html')
];

function listFiles() {
  var wiredepOptions = _.extend({}, conf.wiredep, {
    dependencies: true,
    devDependencies: true
  });

  return wiredep(wiredepOptions).js
    .concat([
      path.join(conf.paths.src, '/app/**/*.module.js'),
      path.join(conf.paths.src, '/app/**/*.js'),
      path.join(conf.paths.src, '/**/*.spec.js'),
      path.join(conf.paths.src, '/**/*.mock.js'),
      path.join(conf.paths.src_test, '/app/**/*.module.js'),
      path.join(conf.paths.src_test, '/app/**/*.js'),
      path.join(conf.paths.src_test, '/**/*.spec.js'),
      path.join(conf.paths.src_test, '/**/*.mock.js')
    ])
    .concat(pathSrcHtml);
}

module.exports = function(config) {

  var configuration = {
    files: listFiles(),

    singleRun: true,
	
    colors:    false,

    autoWatch: false,

    ngHtml2JsPreprocessor: {
      stripPrefix: conf.paths.src + '/',
      moduleName: 'TODO_PUT_HERE_YOUR_MODULE_NAME'
    },

    logLevel: 'WARN',

    frameworks: ['jasmine', 'angular-filesort'],

    angularFilesort: {
      whitelist: [path.join(conf.paths.src, '/**/!(*.html|*.spec|*.mock).js'), path.join(conf.paths.src_test, '/**/!(*.html|*.spec|*.mock).js')]
    },

    browsers: ['PhantomJS'],

    sonarQubeUnitReporter: {
      sonarQubeVersion: 'LATEST',
      outputFile: 'reports/ut_report.xml',
      useBrowserName: false
    },

    plugins: [
      'karma-phantomjs-launcher',
      'karma-angular-filesort',
      'karma-coverage',
      'karma-jasmine',
      'karma-ng-html2js-preprocessor',
      'karma-sonarqube-unit-reporter'
    ],

    coverageReporter: {
      type : 'lcov',
      dir : 'reports',
      subdir : 'coverage'
    },

    reporters: ['progress', 'sonarqubeUnit', 'coverage'],
    
    preprocessors: {
      'src/**/*.js':   ['coverage'],
      'test/**/*.js':   ['coverage']
    }
  };

  config.set(configuration);
};
```

By default, the description of the jasmine tests used as the path attribute in the generated xml. If this is not the case with your tests, you can use the following options to automagically find the right path values. It is the recommended way to use this plugin but to be backward compatible it is not enabled by default.

```
sonarQubeUnitReporter: {
      sonarQubeVersion: 'LATEST',
      outputFile: 'reports/ut_report.xml',
      overrideTestDescription: true,
      testPaths: ['./test', './moreTests'],
      testFilePattern: '.spec.js',
      useBrowserName: false
},
```

##### How to build

* NodeJS 8.0.0 https://nodejs.org/download/release/v8.0.0/
* npm install
* npm build