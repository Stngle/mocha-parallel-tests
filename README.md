# mocha-parallel-tests

[![Build Status](https://img.shields.io/travis/yandex/mocha-parallel-tests/master.svg?style=flat)](https://travis-ci.org/yandex/mocha-parallel-tests)

`mocha-parallel-tests` is a test runner for tests written with `mocha` testing framework which allows you to run them in parallel. `mocha-parallel-tests` executes **each of your test files in a separate process** while maintaining the output structure of `mocha`. Compared to the other tools which try to parallelize `mocha` tests execution, `mocha-parallel-tests` doesn't require you to write the code in a different way or use some specific APIs - just run your tests with `mocha-parallel-tests` instead of `mocha` and you will see the difference. Or if you prefer to use `mocha` programmatic API replace it with `mocha-parallel-tests` default export and you're done!

## Installation

`npm install --save-dev mocha mocha-parallel-tests`

**ATTENTION**: `mocha` is a peer dependency of `mocha-parallel-tests` so you also need to install `mocha`. Currently `mocha` versions 3, 4 and 5 are supported.

## Usage

### CLI

```bash
# mocha example
$ mocha -R xunit --timeout 10000 --slow 1000 test/*.spec.js

# mocha-parallel-tests example
$ mocha-parallel-tests -R xunit --timeout 10000 --slow 1000 test/*.spec.js
```

Most of `mocha` CLI options are supported. If you're missing some of the options support you're welcome to submit a PR: all options are applied in a same simple way.

### Programmatic API

```javascript
// mocha example
import * as Mocha from 'mocha';
const mocha = new Mocha();
mocha.addFile(`${__dirname}/index.spec.js`);
mocha.run();

// mocha-parallel-tests example
// if you're using TypeScript you don't need to install @types/mocha-parallel-tests
// because package comes with typings in it
import Mocha from 'mocha-parallel-tests'; // or `const Mocha = require('mocha-parallel-tests').default` if you're using CommonJS
const mocha = new Mocha();
mocha.addFile(`${__dirname}/index.spec.js`);
mocha.run();
```

## Parallel limit

`mocha-parallel-tests` CLI executable has its own `--max-parallel` option which is the amount of tests executed at the same time. By default it's equal to the number of logical CPI cores (`os.cpus().length`) on your computer but you can also specify your own number or set it to 0, which means that all test files will be started executing at the same time. However this is not recommended especially on machines with low number of CPUs and big number of tests executed.

## Differences with mocha

Main difference with `mocha` comes from the fact that all files are executed in separate processes and don't share the scope. This means that even global variables values that you could've used to share the data between test suites will not be reliable. There's also some specific behaviour for some of the `mocha` CLI options like `--bail`: it's just applied to each test in its process. You can see the full list of differences [here](https://github.com/yandex/mocha-parallel-tests/wiki/Differences-with-mocha).

From the reporter perspective the main difference between tests executed with `mocha` and `mocha-parallel-tests` is another level of nesting which again comes from the fact that main process adds one more "suite" level and all tests results are merged into that:

**mocha**

<kbd>
  <img src="https://user-images.githubusercontent.com/73191/40331528-0b79999c-5d94-11e8-86ba-ca5213c9fe29.png" alt="mocha spec reporter output"/>
</kbd>

**mocha-parallel-tests**

<kbd>
  <img src="https://user-images.githubusercontent.com/73191/40331597-39f89b38-5d94-11e8-9388-c42a27b0da9b.png" alt="mocha-parallel-tests spec reporter output"/>
</kbd>
