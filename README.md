# Alexa

I've put this together in an attempt to show how to get an Alexa skill working in multiple regions (including Australia) using the CLI as the documentation available is rather vague.

## Prerequisites
1. You have an IAM user configured with the appropriate role.
1. You have the ASK CLI installed
1. You have initialised your ASK CLI profile with the `ask init` command

## New skill
1. Create the boilerplate files/code using the default HelloWorld example

```
ask new -n <skillName>
```

2. Copy the model for the required region

```
cd <skillName>/models
cp en-US.json en-AU.json
```
3. Replicate the locale in the `skill.json` file


```
        "en-US": {
          "summary": "Sample Short Description",
          "examplePhrases": [
            "Alexa open hello world",
            "Alexa tell hello world hello",
            "Alexa ask hello world say hello"
          ],
          "name": "HelloWorld",
          "description": "Sample Full Description"
        },
        "en-AU": {
          "summary": "Sample Short Description",
          "examplePhrases": [
            "Alexa open hello world",
            "Alexa tell hello world hello",
            "Alexa ask hello world say hello"
          ],
          "name": "HelloWorld",
          "description": "Sample Full Description"
        }

```
4. Add the endpoints for each region in the apis element
```
    "apis": {
      "custom": {
        "endpoint": {
          "sourceDir": "lambda/custom"
        },
        "regions": {
          "NA": {
            "endpoint": {
              "sourceDir": "lambda/custom",
              "uri": "ask-AlexaSkillHelloWorld-NA"
            }
          },
          "FE": {
            "endpoint": {
              "sourceDir": "lambda/custom",
              "uri": "ask-AlexaSkillHelloWorld-AU"
            }
          }
        }
      }
    },

```
5. Deploy the Alexa skill
```
ask deploy -p <profile>
```

## Update Lambda function code
The ask cli is a bit buggy and doesn't seem to allow deploying later versions of the lambda code to other regions outside of `us-east-1`

Revert to using the `aws cli` command instead
1. Create a zip file of the custom code
```
cd lambda/custom
zip -r ../../index.zip *
cd ../..
aws lambda update-function-code --function-name <your function name> --profile <your aws profile> --zip-file fileb://index.zip --region <region to deploy to>
```