# API Gateway + Lambda TOKEN Authorizer Example

This example shows you how to create an API Gateway API with a Lambda TOKEN Authorizer using SAM.

The Authorizer Lambda Function in this example simply accepts an `Authorization` header with valid values `"allow"` and `"deny"`. See [Introducing custom authorizers in Amazon API Gateway](https://aws.amazon.com/blogs/compute/introducing-custom-authorizers-in-amazon-api-gateway/) for a more detailed example using JWT.

## Running the example

Optional: Uncomment the following lines in `template.yaml` to enable a publicly accessible endpoint:

```yaml
# Auth:
#   Authorizer: NONE
```

Deploy the example into your account:

```bash
# Replace APP_NAME with the name of your application and your AWS credential profile in scripts/config.sh
sh scripts/deploy.sh
```

Invoke the API's root endpoint `/` without an `Authorization` header to see the API respond with a 200 (assuming you followed the optional step above). In the SAM template, we explicitly state `Authorizer: NONE` to make this a public/open endpoint (the Authorizer Lambda Function is not invoked).

```bash
curl "$(sh scripts/describe-stack-api-url.sh)"
```

Invoke the API's `/users` endpoint without an `Authorization` header to see a `401 {"message":"Unauthorized"}` response. Since we didn't specify an `Authorization` header, API Gateway didn't even attempt to invoke our Authorizer Lambda Function. If you specify a `ValidationExpression` on the Authorizer, it will also not invoke the Authorizer Lambda Function if the header was provided but does not match the pattern.

```bash
curl "$(sh scripts/describe-stack-api-url.sh)users"
```

Invoke the API's `/users` endpoint with an `Authorization: allow` header to see a `200` response. Try changing the header value to `"deny"` to see a `403` response and `"gibberish"` to see a `500` response.

```bash
curl "$(sh scripts/describe-stack-api-url.sh)users" -H 'Authorization: allow'
```

## Additional resources

- https://aws.amazon.com/blogs/compute/introducing-custom-authorizers-in-amazon-api-gateway/
- https://docs.aws.amazon.com/apigateway/latest/developerguide/apigateway-use-lambda-authorizer.html
