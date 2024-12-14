# REST API & Its Design
## What is REST API?
REST (Representational State Transfer) is an architectural style for designing networked applications. It is commonly used for building APIs that allow clients to communicate with servers. They are designed to be lightweight, stateless and scalable making them ideal for web and mobile apps. Each request from the client to a server must contain all information needed to understand and process the request. Each request is independent and server doesn't remember the previous requests. Client-Server Architecture is when the client and the server are independent of each other. When the client sends a request, server processes it and sends back the appropriate response. REST relies on a constant set of methods to interact with resources, using HTTP methods. They define the actions a client can perform on the server.
## REST API Design
Designing a REST API involves creating endpoints and steps on how the client and server will communicate. There are many considerations on practices for designing a REST API.
### Meaningful and Consistent URIs
Structure of API endpoints should reflect sources being manipulated and should follow consistent conventions. One of the common example is a resource called 'users', which has an API endpoints following like this list:
* `GET /users` = Retrieve a list of users
* `POST /users` = Create a new user
* `GET /users/{id}` = Retrieve a specific user by ID
* `PUT /users/{id}` = Update an existing user by ID
* `DELETE /users/{id}` = Delete a user by ID
* `PATCH /users//{id}` = Update users email. PATCH is applied for partial updates to a resource, so you don't have to send whole user data in the fields that is going to be updated.
```json
{
  "email":"newemail@example.com"
}
```
## JSON / XML
REST APIs typically use JSON as the data format for communication between a client and a server. JSON format is easy to parse
```json
{
    "id":1,
    "name":"User123",
    "email":"user@example.com"
}
```
## Relationship Between Resources in URL 
`/users/{id}/orders`, Orders belonging to a specific user based on ID
`/categories/{categoryID}/products`, Products in a specific category
## Hypermedia Links
Used to guide client through the available actions or next steps.
```json
{
    "id":1,
    "name":"user",
    "links": {
        "self":"/users/1",
        "orders":"/users/1/orders"
    }
}
```
## Get List of Users
Use `GET /users`
```json
[
    {
        "id":1,
        "name":"user1", 
        "email":"user1@example.com"
    },
    {
        "id":2,
        "name":"user2", 
        "email":"user2@example.com"
    }
]
```