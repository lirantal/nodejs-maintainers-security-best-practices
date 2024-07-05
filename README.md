# Node.js Maintainers Security Best Practices

A cureated collection of security best practices for Open Source software maintainers 

## Code

### Do not pass API options object to Node.js core API objects

Do not mix your own program interface that is exposed to developer/user input with that of sensitive Node.js APIs.

For example, if your program interface exposes an options object on how to parse Git statistics, such as `{markdown: false}` and the program
implementation detail is to use the `child_process.execFile` API. Do not pass that options object as is to `execFile()` because users may
inadvertently pass user input as-is, which may include `{shell: true}` and result in interactive shell interpolation of special characters
as part of the process execution.

Case in question is the `git-contributors` package which would allow the following:

```js
var GitContributors = require('git-contributors').GitContributors;

var shellCommand = "./shell.sh";

opts = { cwd: "/tmp/chmod-755", markdown: false, shell: shellCommand };

GitContributors.list(opts, function (err, result) {
  if (err) {
    throw err;
  }
  console.log(JSON.stringify(result, null, 2));
});
```

### Do not use `property in object` 

Rational ([mdn source](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/hasOwn)): 

```js
const example = {};
example.prop = "exists";

// The `in` operator will return true for direct or inherited properties:
"prop" in example; // true
"toString" in example; // true
```

Avoid this pattern due to prototype pollution vulnerabilities.

### Harden against Prototype Pollution vulnerabilities

Instead of:

```js
if (options.isAdmin) {}
```

Prefer:

```js
if (Object.hasOwn(options, 'isAdmin') {}
```

This isn't intuitive and verbose, and so, you may reserve this pattern for sensitive object properties only.

## CI 

### Do not use unknown Actions for GitHub Actions 

### Do not use 

### Always pin the GitHub Action source using a SHA

