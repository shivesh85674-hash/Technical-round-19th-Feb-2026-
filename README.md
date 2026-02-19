# Technical-round-19th-Feb-2026-

// server.js (server creation)
const express = require('express');
const mongoose = require('mongoose');
const cors = require('cors');

const app = express();
app.use(cors());
app.use(express.json());

// MongoDB connection (Mongo DB linking)
mongoose.connect('mongodb://localhost:27017/tweetflick', {
    useNewUrlParser: true,
    useUnifiedTopology: true,
});

// Tweet schema/model
const tweetSchema = new mongoose.Schema({
    text: { type: String, required: true, maxlength: 140 },
    createdAt: { type: Date, default: Date.now }
});
const Tweet = mongoose.model('Tweet', tweetSchema);

// GET /api/tweets >> newest first
app.get('/api/tweets', async (req, res) => {
    try {
        const tweets = await Tweet.find().sort({ createdAt: -1 });
        res.json(tweets);
    } catch {
        res.status(500).json({ error: 'Failed to fetch tweets' });
    }
});

// POST /api/tweets
app.post('/api/tweets', async (req, res) => {
    const text = (req.body.text || '').trim();
    if (!text || text.length > 140) {
        return res.status(400).json({ error: 'Invalid tweet' });
    }
    try {
        const tweet = new Tweet({ text });
        await tweet.save();
        res.status(201).json(tweet);
    } catch {
        res.status(500).json({ error: 'Failed to post tweet' });
    }
});

// Start server
const PORT = process.env.PORT || 3000;
app.listen(PORT, () => console.log(`Server running on port ${PORT}`));

// TASK 1 : ADD CODE TO CREATE TWEET AND UPLOAD TO Database using fetch POST method
try {
    const res = await fetch('http://localhost:3000/api/tweets', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ text })
    });

    if (res.ok) {
        tweetText.value = '';
        updateCounter();
        loadTweets();
    } else {
        alert('Failed to tweet!');
    }
} catch (error) {
    status.textContent = '❌ Backend offline';
    console.error('Error:', error);
}

// TASK 2 : LOAD TWEETS >> fetch from database and show error message incase of failure - 
try {
    const res = await fetch('http://localhost:3000/api/tweets');
    if (!res.ok) throw new Error('Failed to load');

    const tweets = await res.json();
    renderTweets(tweets);
    status.textContent = `Connected (${tweets.length} tweets)`;
} catch (error) {
    tweetsDiv.innerHTML = '<div style="padding:40px;text-align:center;color:#e0245e">Failed to load tweets 😢</div>';
    status.textContent = '❌ Connection failed';
}
