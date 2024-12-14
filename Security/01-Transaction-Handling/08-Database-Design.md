# Database Design
Database design is a process of creating a detailed data model for a database. This involves defining the structure, organization and relationships between data to ensure efficient storage, retrieval and integrity. When it comes to payment system, you might as well think that you won't need database for a payment system, since someone just implements your payment system API/code and just continue with payments. But nope, database is essential when it comes to tokenization to save user data safely, along with access token/key and sessions. Before designing a database, you should create a list for further requirements. Here is an example:
* __Define Requirements,__ understand what data needs to be stored, processed and accessed. Mark down the goal and scope of the database.
* __Identify Entities & Attributes.__ __Entities__ are objects to store information about the user and others. __Attributes__ on the other hand are properties of an entity (Name, Email, Order, etc.)
* __Define Relationships.__ Determine how entities relate to each other, e.g. how a user can place d=multiple orders and transactions.
* __Normalize Data,__ eliminate redundancy by breaking down tables into smaller, related tables. Apply normal forms like: *1NF:* Ensure atomic values in columns (no lists); *2NF:* Ensure no partial dependencies on composite keys; *3NF:* ensure no transitive dependencies.
* __Primary & Foreign Keys.__ Here, **Primary Key** is a unique identifier for each record in a table. **Foreign Key** on the other hand is a reference to the primary key, but in another table to establish relationships and communications.
## Database Models
There are many database models. For example, **Relational Databases**, where data is stored and organized in tables with rows and columns (e.g. MySQL, PostgreSQL). NoSQL databases are unstructured, *MongoDB* is database that stores values as documents, *Redis* stores data in key-value pairs.
## RESTful API + Database Design
* **Users:** `id`(primary key), `name`, `email`, `password`
* **Products;** `id`(primary ket), `name`, `description`, `price`
* **Orders;** `id`(primary), `user_id`(foreign referencing Users), `total_price`, `order_date`
* **OrderItems:** `id`, `order_id`(foreign referencing Orders), `product_id`(foreign referencing products), `quantity`
### MongoDB Schema
1. **Users Collection**
```json
{
    "_id":"userId",
    "name":"name",
    "email":"email@example.com",
    "password":"hashed pass"
}
```
2. **Products Collection**
```json
{
    "_id":"productId123",
    "name":"item",
    "description":"cool",
    "price":200
}
```
3. **Orders Collection**
```json
{
    "_id":"orderId123",
    "user_id":"userId123",
    "total":200,
    "order_date":"2024-12-14",
    "items":[
        {
            "product_id":"productId123",
            "quantity":2
        }
    ]
}
```
# Storing data in Node.js

id  | user_id | token_value | exp_at
----------------------------------------
1   | 123     | cba123      | 2024-12-15

Since we got in touch with MongoDB, now Redis will be used in the following example:
```bash
npm install redis
```
```js
const redis = require('redis');
const client = redis.createClient();

client.on('connect', () => {
    console.log('connected to redis');
})
// store a token
client.setex('token123', 3600, 'userId_123', (err, reply) => {
    if(err) {console.error(err);}
    else console.log('Token stored ', reply);
});
// retrieve token
client.get('token123', (err, value) => {
    if(err) {console.error(err);}
    else console.log('Token value ', value);
})
```
### Let's still use MongoDB
```bash
npm install mongoose 
```
```js
const mongoose = require('mongoose');
const tokenSchema = new mongoose.Schema({
    userId: String,
    token: String,
    expiresAt: Date
});

const Token = mongoose.model('Token', tokenSchema);

const newToken = new Token({
    userId:'123',
    token:'cba123',
    expiresAt: new Date(Date.now() + 3600 * 1000) // 1 hour 
});

newToken.save().then(() => console.log('Token Saved'));
// Read reminder below
```
**!!!** Once again! Do not log the last line in real, world-wide applications! This can be used for testing in localhost, do not use `.then()` method, since it might provide details to attackers, how your app works.
## When Redis, When MongoDB
Overall, both are good, but even though Redis is fast, use it for temporary data storing, like sessions, and MongoDB for persistent.