# Using as AWS SAM Nested Application

Add this repo as a git submodule inside the root of your SAM project:
`git submodule add <url> authorizer`

Add the app to your template.yaml:

```
Auth0Authorizer:
  Type: AWS::Serverless::Application
  Properties:
    Location: ./authorizer/template.yaml
    Parameters:
      TokenIssuer: !Ref Auth0TokenIssuer
      JwksUri: !Ref Auth0JwksUri
      Audience: !Ref Auth0Audience
```


Add the authorizer to your `AWS::Serverless:Api` resource:

```
MyApi:
  Type: AWS::Serverless::Api
  Properties:
    Auth:
      DefaultAuthorizer: Auth0
      AddDefaultAuthorizerToCorsPreflight: false
      Authorizers:
        Auth0:
          FunctionArn: !GetAtt Auth0Authorizer.Outputs.AuthorizerArn
          FunctionPayloadType: TOKEN
          FunctionInvokeRole: !GetAtt Auth0Authorizer.Outputs.InvokeRoleArn
          Identity:
            ValidationExpression: "^Bearer [-0-9a-zA-Z\\._]*$"
            ReauthorizeEvery: 3600
```
