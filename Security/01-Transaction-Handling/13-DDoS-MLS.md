# DDoS Protection (Distributed Denial of Service)
DoS (Denial of Service) attack is a cyber-attack, where perpetrator seeks to make a machine or a network resource unavailable to its intended users by temporarily or indefinitely disrupting services of a host connected to a network. Service denial is accomplished by flooding the targeted machine or a resource to prevent all requests from being fulfilled. In a DDoS attack, the incoming traffic flooding the victim originates from many different sources. Simply an attacker attempts to block a signle source and requests from a client by flooding a targeted machine. They overwhelm a server, service or a network with flood of internet traffic, making it unavailable for users to access.
## Rate Limiting
```js
const rateLimit = require('express-rate-limit');
const limiter = rateLimit({
    windowMs: 15 * 60 * 1000,
    max: 100,
    message: 'Too many requests! Try again Later.'
});
app.use(limiter);
```
Also use firewalls.

# MLS (Machine Learning Security)
## Machine Learning (ML)
ML is a field in artificial intelligence concerned with the development and study of statistical algorithms that can learn from data and generalize to unseen data and perform tasks without instructions.
## How does machine learning work in security fields?
The cyber threat landscape forces organizations to constantly track and correlate millions of external and internal data points across their infrastructure and users. But this is where machine learning shines, since it can recognize patterns and predict threats in massive data sets, all at machine speed.
1. **Find a threat on a network** | Machine learning detects threats by constantly monitoring the behavior of the network anomalies. ML engine processes massive amounts of data in near real time to discover critical incidents like insider threats, unknown malwares and policy violations
2. **Keep users safe** | ML can predict bad sources online to help prevent from connection to malicious websites. 
3. Basically **Malware Protection**