# Tokenization
Takes in senstive data and encrypts it in different digits and characters, as a key (token), then decrypt that data.
## Implementation
* User submits their payment details, instead of storing the card number, you generate a unique token for this transaction
* This token is associated with the original credit card data, but the card number details is never stored in your servers. This token should be stored only in the database.
* When processing payments later, you send the token to a third-party payment processor via a secure API to complete the payment.

### Node Implementation and Encryption
Using ***crypto*** module for a random token generation.
```javascript
const crypto = require('crypto');

// tgOI - Token Generation 01
function tgOI() {
    return crypto.randomBytes(16).toString('hex');
}

const token = tgOI();
```

### Taking in Credit Card Information and Encrypting It
Briefly, tokenization in the contex of payemnt systems is the process of replacing sensetive data, such as credit card numbers with a randomly generated token that can be used in place of the original data for storage, transmission and processing.
```javascript
const crypto = require('crypto');
const aes256 = require('aes256');

const iv = crypto.randomBytes(16);
const key = 'canbeanythingthatissafeuseonlinesourcesforthis';
const data = {}

function encrypt(data) {
    const cipher = crypto.createCipheriv('aes-256-cbc', key, iv);
    let encrypted = cipher.update(data, 'utf8', 'hex');
    encrypted += cipher.final('hex');

    return encrypted;
};

function credentialsEnc(num) {
    const token = crypto.randomBytes(32).toString('hex');

    const encryptedC = encrypt(num);

    data[token] = {
        encryptedC,
        iv: iv.toString('hex'),
        timestamp: Date.now(),
    };

    return token;
};

function recieveDecr(token) {
    const stored = data[token];
    if(!stored) throw new Error('Invalid Data');

    const decipher = crypto.createDecipheriv('aes-256-cbc', key, Buffer.from(stored.iv, 'hex'));
    let decrypted = decipher.update(stored.encryptedC, 'hex', 'utf8');
    decrypted += decipher.final('utf8');

    return decrypted;
};

// usage
const cardNum = '4111-1111-1111-1111';
const tokenEncr = credentialsEnc(cardNum);

const decryptedNum = recieveDecr(tokenEncr);
```