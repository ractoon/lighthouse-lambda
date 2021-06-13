# AWS Lambda Lighthouse Function

Allows running [Lighthouse](https://developers.google.com/web/tools/lighthouse/) from AWS Lambda.

## Setup

### 1. Creating Chromium Lambda Layer

First you'll need to create a layer that contains Chromium to be able to run Lighthouse with. For that we'll be using the [Chrome AWS Lambda repo](https://github.com/alixaxel/chrome-aws-lambda). In your terminal run:

```
git clone --depth=1 https://github.com/alixaxel/chrome-aws-lambda.git && \
cd chrome-aws-lambda && \
make chrome_aws_lambda.zip
```

To create a `chrome_aws_lambda.zip` file.

You can also download this file from one of that repo's [CI workflow runs](https://github.com/alixaxel/chrome-aws-lambda/actions/workflows/aws.yml?query=is%3Asuccess+branch%3Amaster).

### 2. Create New Layer

Log in to AWS and navigate to [Lambda](https://console.aws.amazon.com/lambda/).

1. In the left sidebar navigation go to Additional Resources > Layers
2. Click the "Create Layer" button
3. Enter a "Name" for this new layer, e.g. chromiumLayer
4. Select "Upload a .zip file" and click the "Upload" button, select your `chrome_aws_lambda.zip` file
5. For "Compatible runtimes" select `Node.js 14.x`
6. Click "Create"

### 3. Create Function Assets

Back in the terminal on your computer run:

```
git clone git@github.com:ractoon/lighthouse-lambda.git
cd lighthouse-lambda
npm install
zip lighthouse-lambda.zip index.js node_modules
```

### 4. Create Lambda Function

1. Navigate to [AWS Lambda](https://console.aws.amazon.com/lambda/) again and in the navigation sidebar go to "Functions"
2. Click the "Create Function" button
3. Select "Author from scratch" and enter a function name
4. Select `Node.js 14.x` as the runtime
5. Click "Create function"
6. After the function is created, in the "Code source" section select "Upload from" and choose ".zip file", select the `lighthouse-lambda.zip` file you created above
7. In the "Layers" section click "Add a layer"
8. Click on "Custom layers"
9. Select the layer you created above, and click "Add"


### 5. Lambda Resources

In order to run Chromium the lambda configuration will need to be updated with some additional memory, and an increased timeout.

1. In the "Configuration" tab click on "General configuration", and click "Edit"
2. Within the "General configuration" panel set the memory to `1600MB` and timeout to `5 minutes`
3. Click "Save"

### 6. Testing Your Function

In the "Test" tab create a new test event with the body:

```
{
  "url": "https://www.google.com"
}
```

Save your changes and then hit the "Test" button. It will run your function and should return "Execution result: succeeded" with the details containing the JSON result from lighthouse.

## Configuration

The `url` parameter is required to retrieve lighthouse results. You can also pass `options` which accepts lighthouse flags.