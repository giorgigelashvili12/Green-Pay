# Authentication
Basically, authentication is a process of verifying the identity of a user. It's one of the first steps in securing an application API, ensuring the user is trying to only and only acess the service claiming as who they are. One of the best examples would be when a user logs in a website using their accounts username and password. This performance is called 'authentication', where you validate given data and credentials to ensure that the person is the user who owns the account.

## Types of Authentications
* **Basic Authentication:** Simple but insecure; User provides a username and a password directly which are usually sent in the HTTP headers. Insecure, because without TLS/HTTPS, credentails are sent in plain text.
* **Session Based:** The server keeps track of users session stored in a session database or memory. When a user logs in, the server creates a session ID and stores it in a cookie, which is sent to the user's browser. This session ID is then verified.
* **Token-Based (Stateless):** Scalable and commonly used with APIs and in single-page applications. Instead of keeping tracks of the session, server generates a token after sucessful login. This token sent with each request and server verifies the token to authenticate the user without needing to store sessions.

## JWT for Authentication
JWT (JSON Web Token) is commonly used in token-based authentication. Firstly, user logs in with provided credentials. Then these credentials are verified with different modules for comprasion and safety. If given credentials are valid, server generates a JWT containing user-specific information, such as user ID, roles and permissions in payload. When the user makes a request, for every requeest, the client includes the JWT. Then the server verifies the JWT using the signature to ensure it hasn't been tampered with. If valid, the server allows access to requested source.
### Why JWT?
JWT is stateless, meaning that server doesn't need to store session data. All necessary information is embedded within the token itself. Since the server is stateless, it is also scalable. You can easily scale applications without worrying about managing sessions.
## JWT Structure
JWT has three parts: **Header**, **Payload** and **Signature**
### Header
Typical Header contains data like signing algorithms and others.
```json
{
    "alg":"HS256",
    "typ":"JWT"
}
```
### Payload
Payload on the other hand contains the claims, which are the statements about thr user, such as name, ID and roles. These claims can be:
* Registered Claims (expiration time)
* Public Claims (user claims)
* Private Claims (custom claims for an application)
```json
{
    "sub":"1234567890", // ID
    "name":"User123", // Name
    "iat":123456789, // Issued at time
    "exp":123456789 // expiration timr   
}
```
### Signature
Signature, simply ensures the token hasn't been tampered with.
```javascript
const jwt = require('jsonwebtoken');
const token = jwt.sign({ userId: 123 }, 'key', { expiresIn: '1h' });
```

## Authentication Flow Example
```javascript
const jwt = require('jsonwebtoken');
const bcrypt = require('bcrypt');

const user = { username: 'user123', password: 'passwrd123' };

bcrypt.compare(user.password, hash, (err, result) => {
    if(result) {
        const token = jwt.sign({ userId: 123 }, 'key', { expiresIn: '1h' });
        res.json({ token });
    } else {
        res.status(401).send('Invalid Credentials');
    }
});
```
### Client Fetching JWT
```javascript
fetch('/api/protected', {
    headers: {
        'Authorization': `Bearer ${jwt}`
    };
});
```

### Server Verification
```javascript
jwt.verify(token, 'key', (err, decoded) => {
    if(err) {
        res.status(401).send('Unauthorized');
    } else {
        // payload and other app contents
    }
});
```