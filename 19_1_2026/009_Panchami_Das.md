//Max 5 requests per 10 seconds per IP
const express = require("express");
const redis = require("redis");

const app = express();
const client = redis.createClient(); // connects to localhost:6379

// connect to redis
client.connect();

// rate limiter middleware
const rateLimiter = async (req, res, next) => {
  const ip = req.ip;                 // identify user by IP
  const key = `rate:${ip}`;          // Redis key
  const limit = 5;                   // max requests
  const window = 10;                 // seconds

  const count = await client.incr(key); // INCR is atomic

  if (count === 1) {
    // first request → start expiry window
    await client.expire(key, window);
  }

  if (count > limit) {
    return res.status(429).json({
      error: "Too many requests. Try again later."
    });
  }

  next(); // allow request
};

app.use(rateLimiter);

app.get("/", (req, res) => {
  res.send("Request allowed");
});

app.listen(3001, () => {
  console.log("Server running on port 3001");
});
