const express = require('express');
const bodyParser = require('body-parser');
const app = express();
const port = 3000;

// Dummy data for books and reviews (replace with your actual data source)
let books = [
    { id: 1, title: 'Book 1', author: 'Author 1', isbn: '1234567890' },
    { id: 2, title: 'Book 2', author: 'Author 2', isbn: '0987654321' }
];
let reviews = [];

// Middleware for parsing JSON requests
app.use(bodyParser.json());

// Middleware for authentication
const authenticateUser = (req, res, next) => {
    // Check if user is logged in (you can use sessions or JWT here)
    const isLoggedIn = true; // Dummy check, replace with actual logic
    if (isLoggedIn) {
        next(); // User is authenticated, proceed to the next middleware or route handler
    } else {
        res.status(401).send('Unauthorized'); // User is not authenticated
    }
};

// Task 1: Get the book list available in the shop
app.get('/books', async (req, res) => {
    res.json(books);
});

// Task 2: Get the books based on ISBN
app.get('/books/isbn/:isbn', async (req, res) => {
    const isbn = req.params.isbn;
    const book = books.find(book => book.isbn === isbn);
    if (book) {
        res.json(book);
    } else {
        res.status(404).send('Book not found');
    }
});

// Task 3: Get all books by Author
app.get('/books/author/:author', async (req, res) => {
    const author = req.params.author;
    const booksByAuthor = books.filter(book => book.author === author);
    res.json(booksByAuthor);
});

// Task 4: Get all books based on Title
app.get('/books/title/:title', async (req, res) => {
    const title = req.params.title;
    const booksWithTitle = books.filter(book => book.title.toLowerCase().includes(title.toLowerCase()));
    res.json(booksWithTitle);
});

// Task 5: Get book Review
app.get('/reviews/:bookId', async (req, res) => {
    const bookId = parseInt(req.params.bookId);
    const bookReviews = reviews.filter(review => review.bookId === bookId);
    res.json(bookReviews);
});

// Task 6: Register New user
app.post('/register', async (req, res) => {
    // Register user logic here
    res.status(201).send('User registered successfully');
});

// Task 7: Login as a Registered user
app.post('/login', async (req, res) => {
    // Login logic here
    res.status(200).send('User logged in successfully');
});

// Task 8: Add/Modify a book review (for logged-in users)
app.post('/reviews', authenticateUser, async (req, res) => {
    const { userId, bookId, rating, comment } = req.body;
    // Check if the user has already reviewed the book
    const existingReviewIndex = reviews.findIndex(review => review.userId === userId && review.bookId === bookId);
    if (existingReviewIndex !== -1) {
        // Modify existing review
        reviews[existingReviewIndex] = { userId, bookId, rating, comment };
        res.status(200).send('Review modified successfully');
    } else {
        // Add new review
        reviews.push({ userId, bookId, rating, comment });
        res.status(201).send('Review added successfully');
    }
});

// Task 9: Delete book review added by that particular user (for logged-in users)
app.delete('/reviews/:bookId', authenticateUser, async (req, res) => {
    const bookId = parseInt(req.params.bookId);
    const userId = req.body.userId; // Assuming userId is sent in the request body
    const reviewIndex = reviews.findIndex(review => review.userId === userId && review.bookId === bookId);
    if (reviewIndex !== -1) {
        reviews.splice(reviewIndex, 1);
        res.status(200).send('Review deleted successfully');
    } else {
        res.status(404).send('Review not found');
    }
});

// Start the server
app.listen(port, () => {
    console.log(`Server running at http://localhost:${port}`);
});
