## data model

```js
const mongoose = require('mongoose');

// User schema
const userSchema = new mongoose.Schema({
  name: { type: String, required: true },
  email: { type: String, required: true },
  password: { type: String, required: true },
  address: { type: String },
  phone: { type: String },
  role: { type: Number, default: 0 }, // 0: regular user, 1: admin
  profilePicture: { type: String }, // Field to store the profile picture
  // Additional fields as per your requirements
});

// Book schema
const bookSchema = new mongoose.Schema({
  title: { type: String, required: true },
  author: { type: String, required: true },
  description: { type: String },
  genre: [{ type: String }],
  price: { type: Number, required: true },
  isAvailable: { type: Boolean, default: true },
  borrowerList: [{ type: mongoose.Schema.Types.ObjectId, ref: 'BorrowerListItem' }],
  returnedList: [{ type: mongoose.Schema.Types.ObjectId, ref: 'ReturnedBook' }],
  quantity: { type: Number, required: true },
  quantityForSale: {
    type: Number,
    required: true,
    validate: {
      validator: function (v) {
        return v + (this.quantityForBorrow || 0) === this.quantity;
      },
      message: 'The sum of quantityForSale and quantityForBorrow should be equal to quantity.',
    },
  },
  quantityForBorrow: {
    type: Number,
    required: true,
    validate: {
      validator: function (v) {
        return v + (this.quantityForSale || 0) === this.quantity;
      },
      message: 'The sum of quantityForSale and quantityForBorrow should be equal to quantity.',
    },
  },
  soldQuantity: { type: Number, default: 0 },
  borrowedQuantity: { type: Number, default: 0 },
  // Additional fields as per your requirements
});



// Borrower List Item schema
const borrowerListItemSchema = new mongoose.Schema({
  borrower: { type: mongoose.Schema.Types.ObjectId, ref: 'User' },
  duration: {
    type: String,
    enum: ['7d', '15d', '30d'],
    required: true,
  },
});

// Returned Book schema
const returnedBookSchema = new mongoose.Schema({
  book: { type: mongoose.Schema.Types.ObjectId, ref: 'Book', required: true },
  userId: { type: mongoose.Schema.Types.ObjectId, ref: 'User', required: true },
  returnDate: { type: Date, default: Date.now },
});

// Transaction schema
const transactionSchema = new mongoose.Schema({
  book: { type: mongoose.Schema.Types.ObjectId, ref: 'Book', required: true },
  borrower: { type: mongoose.Schema.Types.ObjectId, ref: 'User' },
  seller: { type: mongoose.Schema.Types.ObjectId, ref: 'User' },
  transactionType: {
    type: String,
    enum: ['borrow', 'soldByCash', 'soldOnline'],
    required: true,
  },
  transactionDate: { type: Date, default: Date.now },
  // Additional fields as per your requirements
});

// Define models
const User = mongoose.model('User', userSchema);
const Book = mongoose.model('Book', bookSchema);
const BorrowerListItem = mongoose.model('BorrowerListItem', borrowerListItemSchema);
const ReturnedBook = mongoose.model('ReturnedBook', returnedBookSchema);
const Transaction = mongoose.model('Transaction', transactionSchema);

module.exports = { User, Book, BorrowerListItem, Transaction };

```

> #### explaining book schema

Book Schema:

- title: String (required)
  - Represents the title of the book.

- author: String (required)
  - Represents the author of the book.

- description: String
  - Provides a description of the book.

- genre: Array of strings
  - Represents the genre(s) of the book.

- price: Number (required)
  - Represents the price of the book.

- isAvailable: Boolean (default: true)
  - Indicates the availability status of the book.
  - True: Book is available for borrowing/sale.
  - False: Book is currently unavailable.

- borrowerList: Array of ObjectId (ref: 'BorrowerListItem')
  - Stores the references to BorrowerListItem documents.
  - Represents the list of borrowers who borrowed this book.
  - Each BorrowerListItem document contains borrower and duration details.

- returnedList: Array of ObjectId (ref: 'ReturnedBook')
  - Stores the references to ReturnedBook documents.
  - Represents the list of returned books.
  - Each ReturnedBook document contains book, userId, and returnDate fields.

- quantity: Number (required)
  - Represents the total quantity of this book available.

- quantityForSale: Number (required)
  - Represents the quantity of this book available for sale.
  - The sum of quantityForSale and quantityForBorrow should be equal to quantity.

- quantityForBorrow: Number (required)
  - Represents the quantity of this book available for borrowing.
  - The sum of quantityForSale and quantityForBorrow should be equal to quantity.

- soldQuantity: Number (default: 0)
  - Represents the total number of this book sold.

- borrowedQuantity: Number (default: 0)
  - Represents the total number of this book borrowed by people.

