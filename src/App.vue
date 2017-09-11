<template>
  <div id="app" class="container">

    <!-- Header -->
    <div class="page-header">
      <h1>Vue.js 2 and Firebase Library Application</h1>
    </div>


    <!-- New Book form -->
    <div class="panel panel-default">

      <!-- Header -->
      <div class="panel-heading">
        <h3><span class="fa fa-plus-circle fa-fw"></span> Add Book</h3>
      </div>

      <!-- Form -->
      <div class="panel-body">
        <form id="form" class="" v-on:submit.prevent >

          <!-- Title -->
          <div class="input-group">
            <span class="input-group-addon">Title</span>
            <input id="book-title" class="form-control" type="text" value="" ref="title" v-model="newBook.title">
          </div>

          <!-- Author -->
          <div class="input-group">
            <span class="input-group-addon">Author</span>
            <input id="book-author" class="form-control" type="text" value="" v-model="newBook.author">
          </div>

          <!-- URL -->
          <div class="input-group">
            <span class="input-group-addon">URL</span>
            <input id="book-url" class="form-control" type="text" value="" v-model="newBook.url">
          </div>

          <!-- Submit Button -->
          <div class="buttons pull-right">
            <button class="btn btn-default" v-on:click="reset" name="" value=""><span class="fa fa-ban fa-fw"></span> Cancel</button>
            <button class="btn btn-primary" v-on:click="addBook" type="submit" name="" value=""><span class="fa fa-plus-circle fa-fw"></span> Add Book</button>
          </div>

        </form>
      </div>
    </div>


    <!-- Book lists -->
    <div class="panel panel-default">

      <!-- Header -->
      <div class="panel-heading">
        <h3><span class="fa fa-book fa-fw"></span> Book Lists</h3>
      </div>

      <!-- Table -->
      <div class="panel-body">

        <table class="table table-striped">
          <thead>
            <tr>
              <th>Title</th>
              <th>Author</th>
              <th>Delete</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="book in books">
              <td><a :href="book.url" target="_blank">{{ book.title }}</a></td>
              <td>{{ book.author }}</td>
              <td class="text-center"><span class="fa fa-trash fa-fw removeBook" v-on:click="removeBook(book)"></span></td>
            </tr>
          </tbody>
        </table>

      </div>


    </div>
  </div>
</template>



<script>

/* Imports Components ---------------------------------------- */

import FireBase from 'firebase' // Database
import toastr from 'toastr' // Notifications


/* Firebase connection config -------------------------------- */

let fbconfig = {
  apiKey: "AIzaSyD4WCdsCDvc9mbZi4wigOLtXX8z9pg720w",
  authDomain: "vuejs-library-c51ba.firebaseapp.com",
  databaseURL: "https://vuejs-library-c51ba.firebaseio.com",
  projectId: "vuejs-library-c51ba",
  storageBucket: "vuejs-library-c51ba.appspot.com",
  messagingSenderId: "102475677721"
}

let fbapp = FireBase.initializeApp(fbconfig) // Initialize Firebase by passing the config object
let fbdb = fbapp.database() // Get the database
let booksRef = fbdb.ref('books') // Get the book node

/* Component instance --------------------------------------- */

export default {
  name: 'app',

  firebase: {
    books: booksRef
  },

  data: function() {
    return {
      // Connected to the new book submit form
      newBook: {
        title: '',
        author: '',
        url: ''
      }
    }
  },

  methods: {
    // Cancels new book data entry
    reset: function() {
      if(this.newBook.title === '' && this.newBook.author === '' && this.newBook.url === '') {
        toastr.warning('Nothing to cancel');
      }

      this.newBook.title = '';
      this.newBook.author = '';
      this.newBook.url = '';

      // Focus the first form input
      this.$refs.title.focus();
    },

    // Add new book entry to the database
    addBook: function() {
      booksRef.push(this.newBook);
      this.reset();
      toastr.success("New Book Added!");
    },

    // Removes existing book entry to the table and database
    removeBook: function(book) {
      booksRef.child(book['.key']).remove();
      toastr.success(`${book.title} was removed succesfully`);

      // Norifies the remaining book count
      // toastr.warning(this.getBookCount() + ' books remaining!');
    },

    // Returns the number of existing books
    getBookCount: function() {
      return this.books.length
    }
  }
}


</script>



<style scoped>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  /*text-align: center;*/
  color: #2c3e50;
  margin-top: 60px;
}

h3 {
  margin: 10px 0;
}

.input-group {
  margin-bottom: 10px;
}
.input-group:last-child {
  margin-bottom: 0px;
}

.input-group-addon {
  min-width: 80px;
}

.removeBook:hover {
  cursor: pointer;
}

</style>
