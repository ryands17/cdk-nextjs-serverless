# cdk-nextjs-serverless

**Note: This package is under development and in alpha. Feel free to experiment with it and leave issues but do not expect it to be production-ready at this time.**

cdk-nextjs-serverless is a Level 3/Pattern construct for deploying NextJS applications to AWS on CloudFront, Lambda@Edge, and S3 for a completely serverless frontend stack. Currently, the package supports only CloudFront deployment utilizing the same build process as Serverless Framework's [NextJS at the Edge component](https://www.serverless.com/blog/serverless-nextjs). As I continue to develop the project, I plan to support per-page Lambdas and an option for deploying to API Gateway.

*Note: this package does not utilize the serverless framework CLI to deploy resources - it is fully written and deployed in CDK-generated CloudFormation.*

## Installation

Use the [npm](https://www.npmjs.com/) or [yarn](https://yarnpkg.com/) package manager to install the package.

```bash
npm i cdk-nextjs-serverless
```

## Usage

### Pre-Requisites

1. `target: 'serverless'` is in your `next.config.js`.
2. Your project has been configured with CDK.
3. `next` is installed (you can check that `node_modules/.bin/next` is there to ensure it will be compatible)

### Basic Usage

At a minimum, specify the location of your Next app directory, and the construct will build your next app and generate the required resources:

```ts
new NextJSServerless(this, 'NextJSApp', {
  nextJSDir: 'next-app' // Relative to CDK project root
})
```
The construct will attempt to locate the nearest parent `node_modules` directory of the supplied `nextJSDir`. If your `next` package is installed in a different `node_modules` directory or the construct cannot find the directory, you can manually specify it:

```ts
new NextJSServerless(this, 'NextJSApp', {
  nextJSDir: 'src/next-app',
  nodeModulesDir: 'node_modules' // Relative to CDK project root
})
```

### Resource Configurations

You can define properties of the generated resources by defining them in the construct props:

```ts
new NextJSServerless(this, 'NextJSApp', {
  nextJSDir: 'next-app',
  lambdaFunctionProps: {
    timeout: Duration.seconds(15),
    memorySize: 1500
  },
  cloudFrontDistributionProps: {
    additionalBehaviors: {
      'npm': {
        origin: origin
      }
    }
  }
})
```

The construct will add your custom props in along with its default props and override custom props for any reserved configurations that are required for the deployment to work.

#### Asynchronous Resource Configurations
Because the construct asynchronously builds your application, the returned object won't yet have its resource properties hydrated. In the case that you'd like to be able to interact with the created resources after initialization, you can use the promise function like so:

```ts
new NextJSServerless(this, 'NextJSApp', {
  nextJSDir: 'next-app',
  lambdaFunctionProps: {
    timeout: Duration.seconds(15),
    memorySize: 1500
  }
})
.promise()
.then(nextJSApp => {
  nextJSApp.cloudFrontWebDistribution?
    .addBehavior('github', new origins.HttpOrigin('https://www.github.com'));
  });
})
```

## Contributing
Pull requests are welcome. For major changes, please open an issue first to discuss what you would like to change.

## License
[MIT](https://choosealicense.com/licenses/mit/)