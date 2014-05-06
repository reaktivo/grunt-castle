# grunt-castle

> Requirejs client, server testing made easy.

A simple working example that will get you up and running quickly can be
found **[here](https://github.com/jstrimpel/grunt-castle-example)**.

## Getting Started
This plugin requires Grunt `~0.4.1`

If you haven't used [Grunt](http://gruntjs.com/) before, be sure to check out the [Getting Started](http://gruntjs.com/getting-started) guide, as it explains how to create a [Gruntfile](http://gruntjs.com/sample-gruntfile) as well as install and use Grunt plugins. Once you're familiar with that process, you may install this plugin with this command:

```shell
npm install grunt-castle --save-dev
```

Once the plugin has been installed, it may be enabled inside your Gruntfile with this line of JavaScript:

```js
grunt.loadNpmTasks('grunt-castle');
```

## The "castle" task

### Overview
In your project's Gruntfile, add a section named `castle` to the data object passed into `grunt.initConfig()`.

```js
grunt.initConfig({
    castle: {
        your_target: {
            // Target-specific file lists and/or options go here.
        },
    },
})
```

### Usage Examples

```js
grunt.initConfig({

    castle: {

        app: { // target name

            options: {

                mocks: {
                    server: {
                        baseUrl: 'test/mocks', // module dependency mocks path
                        paths: {} // mock paths
                    },
                    client: {
                        baseUrl: 'test/mocks', // module dependency mocks path
                        paths: {} // mock paths
                    }
                },

                specs: {
                    baseUrl: 'test/specs', // where to find client/server/common
                    client: 'client/**/*.js', // client only tests
                    server: 'server/**/*.js', // server only tests
                    common: 'common/**/*.js', // common tests
                    'client-target': 'test/specs/html' // location to write client specs
                },

                // coverage and unit testing rely on module paths; if all code is run on both client and server
                // then the baseUrl and paths only need to be defined once under the requirejs property
                requirejs: {
                    server: {
                        baseUrl: './',
                        paths: { // coverage and unit testing rely on module paths
                            a: 'lib/a',
                            x: 'lib/x',
                            y: 'lib/y'
                        }
                    },
                    client: {
                        baseUrl: './',
                        paths: { // coverage and unit testing rely on module paths
                            a: 'lib/a',
                            x: 'lib/x',
                            y: 'lib/y'
                        }
                    }
                },

                reporting: {
                    dest: 'reports', // location to write analysis and coverage reports
                    src: 'lib',
                    options: {},
                    analysis: {},
                    coverage: {
                        dest: 'lib-cov', // target for instrumented code
                        exclude: 'test'
                    }
                }

            }

        }

    }

});
```

#### Executing Tasks
```shell
grunt castle:repo:test # run all client and server unit tests
grunt castle:repo:test-client # run all client unit tests
grunt castle:repo:test-server # run all server unit tests

grunt castle:repo:test:filename # run client and server unit tests for a single file
grunt castle:repo:test-client:filename # run client unit tests for a single file
grunt castle:repo:test-server:filename # run server unit tests for a single file

grunt castle:repo:cov # generate HTML coverage reports for client and server
grunt castle:repo:cov-client # generate HTML coverage reports for client
grunt castle:repo:cov-server # generate HTML coverage reports for server

grunt castle:repo:lcov # generate LCOV files for client and server
grunt castle:repo:lcov-client # generate LCOV files for client
grunt castle:repo:lcov-server # generate LCOV files for server

grunt castle:repo:xunit # generate xunit files for client and server
grunt castle:repo:xunit-client # generate xunit files for client
grunt castle:repo:xunit-server # generate xunit files for server

grunt castle:repo:analysis # run static analysis and complexity reports
```

#### Example Spec
```javascript
describe('Foo Tests', function () {

    var Foo;
    beforeEach(function (done) {
        requirejs(['castle'], function (castle) {
            castle.test({
                module: 'foo', // module to be tested
                mocks: ['baz'], // mock module baz if it is a dependency of module foo
                globals: [{ module: 'bar', exports: 'Bar' }], // any globals needed
                callback: function (module) {
                    Foo = module;
                    done();
                }
            });
        });
    });

    it('1 === 1', function () {
        chai.expect(1).to.be.equal(1);
    });

});
```

## Mock Resolution
Mocks are resolved in the order listed below. Any conflicting paths are overwritten
with the value returned by the resolution method that takes precedence in the order below.

1. Paths that have been defined as part of the `mocks.server.paths`, `mocks.client.paths`
2. Mock file name and mocks baseUrl; this is done for each mock in both environments
3. Application defined paths, `requirejs.server.paths`, `requirejs.client.paths`

## Spec Naming Convention
In order to recieve accurate code coverage for the file you are explicitly testing, your
spec test file path should mirror that of the file it is testing.
#### Example
If testing `path/to/file.js` your spec file should be `test/path/to/file.js`.
