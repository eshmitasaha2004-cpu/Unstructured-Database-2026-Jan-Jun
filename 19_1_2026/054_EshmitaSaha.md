# 🔧 LAB ASSIGNMENT-1 (Redis + Node.js (Express + redis) — Terminal Session (Clean & Formatted))
# ✅ Ctrl+O -> Enter -> Ctrl+X

**Name:** Eshmita Saha
**Roll No:** 054

###  1. Install Redis

sudo apt update
<br>
sudo apt install redis-server
<br>
redis-server

###  Check Redis
redis-cli ping

### Output:
PONG

###  2. SET and GET Key-Value
SET name Eshmita
<br>
GET name

### Output:
"Eshmita"

### 📌 3. INCR Command
SET counter 0
<br>
INCR counter
<br>
INCR counter


### outpur:
1
<br>
2

### 📌 4. EXPIRE Command

SET session active
<br>
EXPIRE session 10
<br>
TTL session

### After 10 seconds:
GET session

### output:
(nil)

### 📌 5. Node.js Server Setup
npm init -y
<br>
npm install express redis

### 📌 6. Server Running
node server.js

### Output:
Server running on port 3000

### 📌 7. Timed Rate Limiter
Access /limited more than 5 times in a minute.

### Output:
Too many requests. Try again later.

### 🧾 server.js Code


```javascript
const express = require("express");
const redis = require("redis");

const app = express();
const client = redis.createClient();

client.connect();

app.get("/", async (req, res) => {
  res.send("Redis Server Running!");
});

// SET and GET example
app.get("/set", async (req, res) => {
  await client.set("username", "Eshmita");
  res.send("Key set successfully");
});

app.get("/get", async (req, res) => {
  const value = await client.get("username");
  res.send(`Value from Redis: ${value}`);
});

// INCR example
app.get("/incr", async (req, res) => {
  const count = await client.incr("counter");
  res.send(`Counter value: ${count}`);
});

// EXPIRE example
app.get("/expire", async (req, res) => {
  await client.set("temp", "This is temporary");
  await client.expire("temp", 10);
  res.send("Key 'temp' will expire in 10 seconds");
});

// Timed Rate Limiter (5 requests per minute per IP)
app.use(async (req, res, next) => {
  const ip = req.ip;
  const key = `rate_limit:${ip}`;

  const requests = await client.incr(key);

  if (requests === 1) {
    await client.expire(key, 60);
  }

  if (requests > 5) {
    return res.status(429).send("Too many requests. Try again later.");
  }

  next();
});

app.get("/limited", (req, res) => {
  res.send("You are within the rate limit!");
});

const PORT = 3000;
app.listen(PORT, () => {
  console.log(`Server running on port ${PORT}`);
});
