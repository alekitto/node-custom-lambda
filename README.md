# Node.js 10.x and 11.x for AWS Lambda

A [custom runtime](https://aws.amazon.com/about-aws/whats-new/2018/11/aws-lambda-now-supports-custom-runtimes-and-layers/)
for AWS Lambda to execute functions in Node.js 10.x or 11.x

## Getting Started

Save as `index.js`:

```js
exports.handler = async(event, context) => {
  console.log(`Hi from Node.js ${process.version} on Lambda!`)
  console.log(`There is ${context.getRemainingTimeInMillis()}ms remaining`)
  return event
}
```

Then bundle up into a zipfile – this is your function bundle:

```sh
zip -yr lambda.zip index.js  # add node_modules too if you have any
```

Create a new Lambda function and choose the custom runtime option.

![Create lambda](https://raw.githubusercontent.com/lambci/node-custom-lambda/master/img/create.png "Create lambda screenshot")

Select your `lambda.zip` as the "Function code" and make the handler "index.handler".

![Function code](https://raw.githubusercontent.com/lambci/node-custom-lambda/master/img/function_code.png "Function code setup screenshot")

Then click on Layers and choose "Add a layer", and "Provide a layer version ARN" and enter the following ARN:

```
arn:aws:lambda:us-east-1:553035198032:layer:nodejs10:6
```

Or [use this link](https://console.aws.amazon.com/lambda/home?region=us-east-1#/connect/layer?layer=arn:aws:lambda:us-east-1:553035198032:layer:nodejs10:1) and pick your function from the "Function name" auto-suggest.

![Add a layer](https://raw.githubusercontent.com/lambci/node-custom-lambda/master/img/layer.png "Add a layer screenshot")

Then save your lambda and test it with a test event!

![Test event output](https://raw.githubusercontent.com/lambci/node-custom-lambda/master/img/log.png "Test event output screenshot")

## Current Version ARNs

| Node.js version | ARN |
| --- | --- |
| 10.15.1 | `arn:aws:lambda:<region>:553035198032:layer:nodejs10:6` |
| 11.8.0 | `arn:aws:lambda:<region>:553035198032:layer:nodejs11:7` |

## Previous Version ARNs

| Node.js version | ARN |
| --- | --- |
| 10.15.0 | `arn:aws:lambda:<region>:553035198032:layer:nodejs10:5` |
| 11.7.0 | `arn:aws:lambda:<region>:553035198032:layer:nodejs11:6` |
| 11.6.0 | `arn:aws:lambda:<region>:553035198032:layer:nodejs11:5` |
| 10.14.2 | `arn:aws:lambda:<region>:553035198032:layer:nodejs10:3` |
| 11.4.0 | `arn:aws:lambda:<region>:553035198032:layer:nodejs11:4` |
| 10.14.1 | `arn:aws:lambda:<region>:553035198032:layer:nodejs10:1` |
| 11.3.0 | `arn:aws:lambda:<region>:553035198032:layer:nodejs11:1` |

## Things to be aware of

* This is a no-batteries-included runtime – you'll need to zip up any
  `node_modules` dependencies, including `aws-sdk` with your lambda function
* It does not monkeypatch `console.log`, `console.error`, etc
  functions to add extra timestamps and request IDs to each line you log as the
  official runtimes do. I believe this leads to fewer surprises and cleaner
  logs that are easier to parse by various tools – but if you're
  relying on this behaviour you'll need to add these fields yourself.
* Cold start overhead of ~270ms for Node.js 10.x and ~290ms for 11.x – this
  is due to Node.js' increasingly slow startup time,
  [but they're working on it!](https://github.com/nodejs/node/issues/17058)
