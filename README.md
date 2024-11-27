# -<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Global Industrial Network</title>
    <link rel="stylesheet" href="style.css">
</head>
<body>
    <header>
        <h1>Welcome to Your Industrial Network</h1>
        <nav>
            <a href="#about">About</a>
            <a href="#companies">Companies</a>
            <a href="#contact">Contact Us</a>
        </nav>
    </header>
    <section id="about">
        <h2>About Us</h2>
        <p>Connecting industries worldwide for seamless collaboration.</p>
    </section>
    <section id="companies">
        <h2>Global Companies</h2>
        <p>Explore companies from all over the world.</p>
        <ul>
            <li>Company A - USA</li>
            <li>Company B - Germany</li>
            <li>Company C - Japan</li>
        </ul>
    </section>
    <section id="contact">
        <h2>Contact Us</h2>
        <form id="contact-form">
            <label for="name">Name:</label>
            <input type="text" id="name" name="name" required>
            
            <label for="email">Email:</label>
            <input type="email" id="email" name="email" required>
            
            <label for="message">Message:</label>
            <textarea id="message" name="message" required></textarea>
            
            <button type="submit">Send</button>
        </form>
    </section>
    <footer>
        <p>&copy; 2024 Your Industrial Network</p>
    </footer>
</body>
</html>
const express = require('express');
const bodyParser = require('body-parser');
const nodemailer = require('nodemailer');
const mysql = require('mysql');

const app = express();
app.use(bodyParser.urlencoded({ extended: true }));
app.use(express.static('public'));

const db = mysql.createConnection({
    host: 'localhost',
    user: 'root',
    password: '',
    database: 'industrial_network'
});

db.connect(err => {
    if (err) throw err;
    console.log('Database connected.');
});

app.post('/send', (req, res) => {
    const { name, email, message } = req.body;
    
    // Save to database
    const query = 'INSERT INTO messages (name, email, message) VALUES (?, ?, ?)';
    db.query(query, [name, email, message], (err) => {
        if (err) throw err;

        // Send email
        const transporter = nodemailer.createTransport({
            service: 'gmail',
            auth: {
                user: 'your_email@gmail.com',
                pass: 'your_email_password'
            }
        });

        const mailOptions = {
            from: email,
            to: 'your_email@gmail.com',
            subject: 'New Contact Form Submission',
            text: `Name: ${name}\nEmail: ${email}\nMessage: ${message}`
        };

        transporter.sendMail(mailOptions, (error, info) => {
            if (error) {
                console.log(error);
                res.send('Error sending email.');
            } else {
                res.send('Message sent successfully!');
            }
        });
    });
});

app.listen(3000, () => console.log('Server running on http://localhost:3000'));
USE industrial_network;

CREATE TABLE messages (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    email VARCHAR(255) NOT NULL,
    message TEXT NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
