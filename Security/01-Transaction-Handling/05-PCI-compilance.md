# PCI Compliance
PCI DSS (Payment Card Industry Data Security Standard) is a set of security standarts designed to ensure that all companies that process, store or transmit credit card information maintain a secure environment. It's important to comply with PCI DSS to protect sensetive data and avoid attacks and reputational damages.
<br/>
PCI DSS consists a set of requirements that organizations must follow to protect credit card data. Basically, the main goal is to ensure that the sensetive data is handled and stored securely, make sure it is not exposed. There are 12 PCI DSS Requirements:
1. Build and Maintain a Secure Network and Systems (Implement firewalls, routers and other security mechanisms to protect cardholder data)
2. Protect Card Data (Use encryption and tokenization methods to protect card numbers when stored. Never store data in plain text)
3. Maintain a Vulnerability Managment Program (KEep software and systems updated to protect against vulnerabilities. Use antiviruses to protect software against a malware)
4. Implement Strong Access Control Measures (Restrict access to card data based on roles)
5. Regularly Monitor Networks (Track and monitor all access to card dara to identify and respond to security vulnerabilities)
6. Maintain an Information Security Policy (Establish a security policy that is regularly updated to reflect the latest risks and best practices)

## PCI DSS Compliance Levels
There are different compliance levels based on the volume of transactions a business processes annually.
* 1. For businesses processing over 6 million credit card transactions per year
* 2. For business processing 1-6 million transactions
* 3. ... Processing 20,000-1 million transactions
* 4. ... processing fewer than 20,000 transactions annually.
For each level there are different requirements.
### For Compliance Usage
You must:
1. Understand Scope (Identify where and how you store, process and transmit cardholder data. Includes your database, web-app and payment systems.)
2. Implement Security Controls (Implement a strong encryption for any credit data you store or transmit. Tokenize card data wherever possible to avoid storing sensetive info. Use firewalls, intrusion detection systems and regular vulnerability scans)
3. Monitor and Audit (Continuosly monitor access to payment systems and card data. Implement audit logs to track any unathorized access or suspicious activity.)
4. PCI Third-Party Services (You can use third-party processors for not doing everything by yourself, reducing PCI scope. But you still have to comply with PCI DSS for your systems)
5. Complete Self-Assessment Audit (Depending on your compliance level, you may need to complete a self-assessment questionnaire or undergo a formal PCI audit perfomed by Qualified Security Assessor)
6. CReate and Maintain Policies + Training (implement security policies that outline your approach to PCI DSS and security in general)
## PCI - Gemeral for This Project
PCI compliance is a huge priority for this project, because handling card data directly opens you up to significant security and legal risks. You won't have to store sensetive credit card data yourself. Instead use tokenization or integrate with a third-party service to handle card details. Also limits PCI scope significantly.
## General Main Security Key-Points
* Tokenize credit card data
* Implement E2EE (Future topic)
* SSL/TLS for all communication, involving sensetive data
* App should be regularly tested (OWASP)
* Complete self-assesment (SAQ)

# Firewalls and Network Security
Firewalls are a next general topic to talk about. They are crucial for protection the boundaries of your network, especially when dealing with sensetive information like credit card informations. Firewall is a security system that monitors and controls incoming and outgoing network traffic based on security rules. It's basically a gatekeeper between your internal and external networks. External firewall protects your network from external threats, and internal one protects sensetive internal sources from being accessed by unauthorized internal users or systems. There are two types of firewalls: Hardware firewalls are physical devices that sit between your network and the internet. Software firewalls are software programs installed on individual servers.
Reminder that you can use `winston` library for error logging, which only developers can see. Use minimal error logging to not leak much information. If an access is blocked, tell the user that the address they're trying to access is not found. If they're trying to inject malicious code, use detections and tell the user that it was an invalid request. For developers, be straight and maximize details.
## Firewall Setup
Firewalls usually:
* Prevent Unauthorized Access
* Block Malicious Traffic
* Restrict Access to Critical Resources
When setting up and implementing firewall in your app, first you have to learn and understand different firewalls and use them based on your application goals:
* Host-Based Firewalls (Protect server where Node app runs)
* Network Firewalls (Secure the network perimeter or isolate the parts of you app structure)
* Web App Firewalls (WAF - Specifically designed to filter HTTP requests, useful for protecting OWASP 10 vulnerabilities)
### Node.js Configurations
#### IP Whitelisting & Blacklisting
You can restrict access to your app by controlling IPs that can send requests.
```javascript
const express = require('express');
const app = express();

const allowedIPs = ['123.123.123.123', '111.111.111.111'];
// trusted IPs
app.use((req, res, next) => {
    const clientIP = req.ip || req.connection.remoteAddress;
    if(!allowedIPs.includes(clientIP)) {
        return res.status(403).send('Not Found');
    };
    next();
});
app.get('/', (req, res) => {
    res.send('Server Running');
});

app.listen(3000);
```
*** Explanation: *** App is created using express, then a list is defined containing trusted IPs for future usage. Then moving on, with app.use() method, function is defined containing main and common parameters like request (req), response (res) and next (move on to next part of the application). Within the anonymous function, another variable is defined, clientIP which contains properties: req.i: retrieves IP address of the client making request. Ensure trust proxy is configures in Express, otherwise, this might not work as expected; Moving on, there's an if statement, which states, that if an array of trusted IPs include the extracted clientIP (Reminder that in this block, there's ! operator included which claims this statement as false), then block access.
#### Rate Limiting
Limit number of requests a client can make to prevent attacks (Using express module)
```javascript
const rateLimit = require('express-rate-limit');
const limiter = rateLimit({
    windowMs: 15 * 60 * 1000,
    max: 100,
    message: 'Try again later.'
});
app.use(limiter);
```
*** Explanation: *** Using a special module, create a limiter, where three values are defined, `windowMs` for time window rate limiting, 900,000ms = 15s, with the help of `max` during this 15 minutes a user can make up to 100 requests, if more than 100 is done, send message, suing `message` saying 'Try again later'. Can might as well send status of 429, too many requests.

#### Secure Headers
Can use `helmet` to add security to headers, which can act like an application-layer firewall for protecting against attacks. The usage is pretty simple:
```javascript
const helmet = require('helmet');
app.use(helmet());
```

#### Web App Firewall (WAF)
You can implement basic WAF-like functionality within Node.js to detect and block malicious patterns in incoming requests. One of the main steps would be thw blocking SQL injections:
```javascript
app.use((req, res, next) => {
    const susPatterns = /(\%27)|(\')|(\-\-)|(\%23)|(#)/i;
    if(susPatterns.test(req,url)) {
        return res.status(400).send('Bad Request');
    }
    next();
});
```

#### Validate & Sanitize Inputs
For less work and maximum validations, you can use `express-validator` to test and sanitize incoming data to make sure it doesn't contain malicious inputs.
```javascript
const { body, validationRes } = require('express-validator');

app.post(
    '/submit',
    body('email').isEmail().withMessage('invalid email format'),
    (req, res) => {
        const errors = validationRes(req);
        if(!errors.isEmpty()) {
            return res.status(400).sjon({ errors: errors.array() });
        }
    };
);
```
#### Protection Against Cross-Site Request Forgery (CSRF)
CSRF is a type of security in a web app where an attacker tricks a user into performing unwanted actions on a website where they are authenticated. An attacker might craft a malicious link or form that performs actions on a user's behalf without their consent.
```javascript
const csurf = require('csurf');
const cookieParser = require('cookie-parser');

app.use(cookieParser());
app.use(csurf({cookie:true}));
app.post('/secure', (req, res) => {
    res.send('CSRF enabled');
});
```
Here, csurf middleware is written, ensuring that every state-changing request includes a valid CSRF token. Using `csurf({cookie:true})` CSRF token is stored in a cookie. Whenever the `/secure` route is acessed, the server cheks the CSRF token provided in the request. If the token is allowed, the request is allowed, otherwise - blocked.
#### API Gateway Integration
API gateway integration is the use of an API gateway to manage route, and monitor API traffic between client and backend services. In Node, an API gateway is typically used to streamline requests, implement security measures and manage microservices. API gateway is a tool thatacts as an intermediary between client and the server.
##### Cloud API Gateway Service (REST API)
```javascript
const express = require('express');
const app = express();
app.use(express.json());

app.get('/users', (req, res) => {
    res.send({ users: ['user1', 'user2'] });
});
app.post('/users', (req, res) => {
    res.status(201).send({ message: 'User Created'});
});
```
##### Consume API
Client makes request to API gateway URL => The gateway routes them to the Node service.
```javascript
const axios = require('axios');
axios.get('https://your-api-gateway-url/users')
    .then(response => console.log(response.data))
    .catch(err => console.error(err));
```
Importing the axios library used to make HTTP requests. After importing, make a GET request to specified URL. Handle request and if it's sucessful response data is logged to the console. `response.data` property contains the payload returned by the API.
#### ENV Based Restrictions
When it comes to sensetive informations, like `port`, MongoDB URI and etc. use environment variables and configure, to restrict sensetive information leak.
```javascript
require('dotenv').config();
```