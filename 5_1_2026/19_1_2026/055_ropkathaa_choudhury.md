# Unstructured Databases Lab
## Date: 19-01-2026

### Name: Ropkathaa Choudhury  
### Roll No: 055  
### Course: B.Tech  
### Department: CSE  

---

## Objective
To install Redis in WSL, perform basic key-value operations, connect Redis with a Node.js server, and implement a time-based rate limiter using Redis.

---

## Redis Installation (WSL)

```bash
sudo apt update
sudo apt install redis-server -y
sudo service redis-server start
redis-cli ping
#O/p -> PONG

SET user:1 Ropkathaa
GET user:1
#o/p -> "Ropkathaa"

SET temp:key hello
EXPIRE temp:key 10
TTL temp:key

GET temp:key

## Node.js and Redis Integration
sudo apt install nodejs npm -y
node -v
npm -v

## Node.js Server Code
const express = require("express");
const redis = require("redis");

const app = express();
const client = redis.createClient();

(async () => {
  await client.connect();
})();

app.use(async (req, res, next) => {
  const key = "rate_limit_test";
  const count = await client.incr(key);

  if (count === 1) {
    await client.expire(key, 10);
  }

  if (count > 3) {
    return res.status(429).send("Rate limit exceeded");
  }

  next();
});

app.get("/set", async (req, res) => {
  await client.set("name", "Ropkathaa");
  res.send("Key set in Redis");
});

app.get("/get", async (req, res) => {
  const value = await client.get("name");
  res.send("Value: " + value);
});

app.listen(3000, () => {
  console.log("Server running on port 3000");
});

## Rate Limiter Verification
Rate limit exceeded

##O/p -> Value: RopkathaaValue: Ropkatredis-cli RopkathaaRate limit exceeded
##Rate limit exceeded
```
