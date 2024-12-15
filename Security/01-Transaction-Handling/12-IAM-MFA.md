# IAM (Identity and Access Managment)
IAM is a framework of policies and technologies to ensure the right individuals have appropriate access to the right resources in an organization or system. IAM has many key points, like **user identification**, wehre managing identities run and making sure so each user has a unique identity. This also involves registration systems along with phone or email verification. **Authentication** is one of the main parts, where you verify users though passwords, or other methods, like 2FA and TOTP. **Authorization** is a partner with authentication, but here, users are granted permissions based on roles or access policies, such as RBAC which is going to be explained further in this readme. **Lifecycle managment** is essential for UI, where creating, updating, deleting work in user accounts based on changes in their roles or system usage. When working with IAM in development, you can use frameworks and libraries like, `Keycloak` which is open-source IAM solution, `AWS IAM` which is for managing users in AWS applications and `Okta/Auth0` for enterprise-level IAM needs.
## IAM: RBAC
Working with IAM RBAC is for restriction access to certain users. In these applications you define roles that you can assign to users. For control you can use middlewares to make sure roles haven't been tampered with.
```js
const express = require('express');
const app = express();

const roles = {
    admin: ['read', 'write', 'delete'],
    user: ['read'],
};

function authorize(role, action) {
    return (req, res, next) => {
        const userRole = req.user.role;
        if(roles[userRole] && roles[userRole].includes(action)) {
            return next();
        }
        return res.status(403).send('Access Denied');
    };
};

app.use((req, res, next) => {
    req.user = {id:1, role:'user'};
    next();
});
app.get('/data', authorize('user', 'read'), (req, res) => {
    res.send('User data');
});
app.post('/data', authorize('admin', 'write'), (req, res) => {
    res.send('data written');
});
```
In this given example RBAC is implemented using Express. Within the code. Roles are defined for further usage. Then authorization middleware is defined which cheks if the user has the necessary role and permission. It recieves the role and action as arguments, then cheks if the user's role allows them to perfrom the requested action. If user passes the statement, move onto the next parts of the app. If it fails, then don't give access to a requested source. After these simulate user authentication with protected routes.
## MFA: TOTP
```js
const express = require('express');
const otplib = require('otplib');
const qrcode = require('qrcode');

const app = express();
app.use(express.json());

const users = {};

app.post('/register', async (req, res) => {
    const userId = req.body.userId;
    const secret = otplib.authenticator.generateSecret();
    users[userId] = { secret };
    
    const otpAuthUrl = otplib.authenticator.keyuri(userId, 'app', secret);
    const qrCode = await qrcode.toDataURL(otpAuthUrl);

    res.json({ secret, qrCode });
});

app.post('/verify', (req, res) => {
    const { userId, token } = req.body;
    const user = users[userId];

    if (!user) {
        return res.status(404).send('User not found');
    }

    const isValid = otplib.authenticator.check(token, user.secret);
    if (isValid) {
        res.send('Token verified');
    } else {
        res.status(401).send('Invalid token');
    }
});

app.listen(3000);
```
## SMS Based OTP
```bash
npm install twilio
```
```js
const twilio = require('twilio');
const client = twilio('ACCOUNT_SID', 'AUTH_TOKEN');

const users = {};

app.post('/send-otp', (req, res) => {
    const phoneNum = req.body.phoneNum;
    const otp = Math.floor(100000 + Math.random()*900000); // 6 digit
    users[phoneNum] = { otp};

    client.messages
        .create({
            body: `Your OTP is ${otp}`,
            from: '+1234567890',
            to: phoneNum,
        })
        .then(() => res.send('OTP sent :-)'));
        .catch(err => res.status(500).send(err));
});
app.post('/verify-otp', (req, res) => {
    const {phoneNum, otp} = req.body;
    const user = users[phoneNum];

    if(user && user.otp === otp) {res.send('OTP verified')}
    else {res.status(401).send('Invalid OTP')};
})
```