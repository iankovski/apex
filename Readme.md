
![Apex Serverless Architecture](assets/logo.png)

Apex is a small tool for deploying and managing [AWS Lambda](https://aws.amazon.com/lambda/) functions. With shims for languages not yet supported by Lambda, you can use Golang out of the box.

## Installation

Download [binaries](https://github.com/apex/apex/releases) or:

```
$ go get github.com/apex/apex/cmd/apex
```

## Runtimes

Currently supports:

- Nodejs
- Golang
- Python

## Features

- Supports languages Lambda does not natively support via shim, such as Go
- Binary install (useful for continuous deployment in CI etc)
- Project level function and resource management
- Command-line function invocation with JSON streams
- Transparently generates a zip for your deploy
- Function rollback support
- Tail function CloudWatchLogs
- Concurrency for quick deploys
- Dry-run to preview changes

## Example

Apex projects are made up of a project.json configuration file, and zero or more Lambda functions defined in the "functions" directory. Here's an example file structure:

```
functions
├── bar
│   ├── function.json
│   └── index.js
├── baz
│   ├── function.json
│   └── index.js
└── foo
    ├── function.json
    └── index.js
project.json
```

The project.json file defines project level configuration that applies to all functions, and defines dependencies. For this simple example the following will do:

```json
{
  "name": "example",
  "description": "Example project"
}
```

Each function uses a function.json configuration file to define function-specific properties such as the runtime, amount of memory allocated, and timeout. For example:

```json
{
  "name": "bar",
  "description": "Node.js example function",
  "runtime": "nodejs",
  "memory": 128,
  "timeout": 5,
  "role": "arn:aws:iam::293503197324:role/lambda"
}
```

Finally the source for the functions themselves look like this in Node.js:

```js
console.log('start bar')
exports.handle = function(e, ctx) {
  ctx.succeed({ hello: 'bar' })
}
```

Or using the Golang Lambda package, Apex supports Golang out of the box with a Node.js shim:

```js
package main

import (
  "encoding/json"

  "github.com/apex/apex"
)

type Message struct {
  Hello string `json:"hello"`
}

func main() {
  apex.HandleFunc(func(event json.RawMessage, ctx *apex.Context) (interface{}, error) {
    return &Message{"baz"}, nil
  })
}
```

Apex operates at the project level, but many commands allow you to specify specific functions. For example you may deploy the entire project with a single command:

```
$ apex deploy
```

Or whitelist functions to deploy:

```
$ apex deploy foo bar
```

## Credentials

Via environment variables:

- `AWS_ACCESS_KEY` AWS account access key
- `AWS_SECRET_KEY` AWS account secret key
- `AWS_REGION` AWS region

Via ~/.aws configuration:

- `AWS_PROFILE` profile name to use
- `AWS_REGION` AWS region (aws-sdk-go does not read ~/.aws/config)

## Links

- [Wiki](https://github.com/apex/apex/wiki)
- [Project Examples](_examples)
- [CLI Examples](https://github.com/apex/apex/blob/master/cmd/apex/apex.go#L46)

## Contributors

- [TJ Holowaychuk](https://github.com/tj)
- [Maciej Winnicki](https://github.com/mthenw)

## Badges

[![Build Status](https://semaphoreci.com/api/v1/projects/d27ff350-b9c5-4d99-96e5-64b1afb441c5/649392/badge.svg)](https://semaphoreci.com/tj/apex)
[![GoDoc](https://godoc.org/github.com/apex/apex?status.svg)](https://godoc.org/github.com/apex/apex)
![](https://img.shields.io/badge/license-MIT-blue.svg)
![](https://img.shields.io/badge/status-experimental-orange.svg)

---

> [tjholowaychuk.com](http://tjholowaychuk.com) &nbsp;&middot;&nbsp;
> GitHub [@tj](https://github.com/tj) &nbsp;&middot;&nbsp;
> Twitter [@tjholowaychuk](https://twitter.com/tjholowaychuk)

