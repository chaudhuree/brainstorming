## data model

```js
const mongoose = require('mongoose');

// User schema
// User schema
const userSchema = new mongoose.Schema({
  name: { type: String, required: true },
  email: { type: String, required: true },
  password: { type: String, required: true },
  address: { type: String, required: false }, // Optional address field
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
  owner: { type: mongoose.Schema.Types.ObjectId, ref: 'User', required: true },
  borrowerList: [
    {
      borrower: { type: mongoose.Schema.Types.ObjectId, ref: 'User' },
      duration: {
        type: String,
        enum: ['7d', '15d', '30d'],
        required: true,
      },
    },
  ],
  returnedList: [
    {
      userId: { type: mongoose.Schema.Types.ObjectId, ref: 'User' },
      returnDate: { type: Date },
    },
  ],
  quantity: { type: Number, default: 1 },
  // Additional fields as per your requirements
});


// Transaction schema
const transactionSchema = new mongoose.Schema({
  book: { type: mongoose.Schema.Types.ObjectId, ref: 'Book', required: true },
  borrower: { type: mongoose.Schema.Types.ObjectId, ref: 'User' },
  seller: { type: mongoose.Schema.Types.ObjectId, ref: 'User' }, // logged in admin id who approved the buy
  transactionType: {
    type: String,
    enum: ['borrow', 'soldByCash', 'soldOnline'],
    required: true,
  },
  transactionDate: { type: Date, default: Date.now },
  // Additional fields as per requirements
});

// Define models
const User = mongoose.model('User', userSchema);
const Book = mongoose.model('Book', bookSchema);
const Transaction = mongoose.model('Transaction', transactionSchema);

module.exports = { User, Book, Transaction };
```
