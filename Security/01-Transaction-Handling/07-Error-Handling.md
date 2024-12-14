# Error Handling
Error handling is important in any API because it helps client understand when something goes wrong and how to handle it. During error handling, you shall use HTTP status code, such as
* `400 Bad Request` - Invalid request data
* `401 Unauthorized` - Authentication is required
* `403 Forbidden` - Don't have permission to perfrom a request
* `404 Not Found` - Resource couldn't be found
* `405 Method Not Allowed` - HTTP method not allowed for a source
* `409 Conflict` - There's a conflict with the current state of the resource
* `422 Unprocessable Entity` - The server understands the request, but the data is invalid or cannot be processed
* `500 Internal Server Error` - Server-side error
* `503 Service Unavailable` - The server is temporarily unavailable
## Error Message Structure
It's important that error responses are consistent, informative and follow a defined structure. But when it comes to safety, it should not contain detailed parts, like when working with tokens, secret keys, and blocked access.
```json
{
    "status":"error",
    "code":400,
    "msg":"Invalid user ID",
    "details":"User must have a positive integer"
}
```
**Curstom Errors**: Some APIs can define a custom error code that provide more granularity. These codes help clients understand what exactly went wrong.
```json 
{
    "status":"error",
    "code":999,
    "msg":"User already exists",
    "details":"Email adress is provided and associated with an existing user"
}
```
**Validation Errors:** When a client sends invalid data, the server should return a 400 bad request status code, with specific detaiks provided to guide the client, what went wrong.
```json
{
    "status":"error",
    "code":400,
    "msg":"Validation Failed",
    "errors": [
        {
            "field":"email",
            "required":true,
            "msg":"Email is required"
        },
        {
            "field":"password",
            "required":true,
            "msg":"Password must be atleast 8 characters long"
        }
    ]
}
```
**Handling Unexpected Errors:** Unexpected errors should be caught by your applications and logged for further inspection. Reminder that you should return a generic error message to the client and don't return detailed errors (stack traces, database errors, etc). When it comes to a 500 status code, return a generic error message like:
```json
{
    "status":"error",
    "code":500,
    "msg":"Something went wrong, please try again later"
}
```
**Error Timeout:** In case of rate limiting or timeouts, the API should return a 429 status code or 408 status, and also include `retry_after` header to let clients know when to retry their request.
```json
{
    "status":"error",
    "code":429,
    "msg":"Too many requests, try again later",
    "retry_after":120
}
```
## Conclusion
**Request**
```json
{
    "email":"invalid-email"
}
```
**Response**
```json
{
    "status":"error",
    "code":400,
    "msg":"Validation failed",
    "errors":[
        {
            "field":"email",
            "required":true,
            "msg":"Email adress is not in a valid format"
        }
    ]
}
```