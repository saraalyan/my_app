<template>
    <div id="book-store">
      <!-- Navigation -->
      <div class="d-flex align-items-baseline justify-content-between bg-dark text-light p-2 m-2">
        <a href="#" style="color:yellow;text-decoration:none;" @click.prevent="isWishlistVisible=false">book</a>
        <div class="align-items-baseline d-flex">
          <p class="mx-1">[{{ wishlist.length }}] <span v-if="wishlist.length === 1">item</span> <span v-else>items</span> in Wishlist</p>
          <button class="btn btn-primary" @click="toggleWishlist">{{ isWishlistVisible ? 'Hide Wishlist' : 'Show Wishlist' }}</button>
        </div>
      </div>
  
      <!-- Books -->
      <div class="row align-items-baseline justify-content-between bg-dark p-2 m-2" v-if="!isWishlistVisible">
        <div class="card mx-auto my-1" v-for="book in books" :key="book.id" style="width: 20rem; margin: 0.2rem;">
          <img :src="book.image" :title="book.name"/>
          <h4 class="my-1 text-center">{{ book.name }}</h4>
          <div class="card-footer">
            <div class="d-flex align-items-baseline justify-content-between">
              <p>Author: {{ book.author }}</p>
              <p :class="getPageColor(book.pages)">Pages: {{ book.pages }}</p>
              <button :class="['btn', isBookInWishlist(book) ? 'btn-danger' : 'btn-primary', 'add-to-wishlist']"
                      :disabled="isBookInWishlist(book)"
                      @click="addToWishlist(book)">
                <span v-if="isBookInWishlist(book)">Already in Wishlist</span>
                <span v-else>Add To Wishlist</span>
              </button>
            </div>
          </div>
        </div>
      </div>
  
      <!-- Wishlist -->
      <div v-if="isWishlistVisible" class="container mt-3">
        <div class="alert alert-danger" role="alert" v-if="wishlist.length === 0">
          <h4 class="alert-heading">Wishlist Empty</h4>
          Your wishlist is currently empty. Add some books!
        </div>
        <div class="watchList" v-if="wishlist.length > 0">
          <table class="table table-hover table-bordered table-striped text-center">
            <thead>
              <tr>
                <th>ID</th>
                <th>ISBN</th>
                <th>Book Name</th>
                <th>Category</th>
                <th>Author</th>
                <th>Pages</th>
                <th>Price</th>
                <th>Actions</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="item in wishlist" :key="item.id">
                <td>{{ item.id }}</td>
                <td>{{ item.ISBN }}</td>
                <td>{{ item.name }}</td>
                <td>{{ item.category }}</td>
                <td>{{ item.author }}</td>
                <td>{{ item.pages }}</td>
                <td>{{ formatCurrency(item.price) }}</td>
                <td>
                  <button class="btn btn-danger" @click="removeFromWishlist(item)">Remove</button>
                </td>
              </tr>
            </tbody>
          </table>
        </div>
      </div>
    </div>
  </template>
  
  <script>
  export default {
    data() {
      return {
        books: [],
        wishlist: [],
        isWishlistVisible: false,
      };
    },
  
    created() {
      this.fetchBooks();
    },

    methods: {
      async fetchBooks() {
        try {
          const response = await fetch("http://localhost:5000/book");
          this.books = await response.json();
        } catch (error) {
          console.error("Error fetching books:", error);
        }
      },
      formatCurrency(value) {
        return new Intl.NumberFormat("ar-SA", {
          style: "currency",
          currency: "SAR",
          minimumFractionDigits: 0
        }).format(value);
      },
      addToWishlist(book) {
        if (!this.isBookInWishlist(book)) {
          this.wishlist.push(book);
          book.inwishlist = true; 
        }
      },
      removeFromWishlist(book) {
        const index = this.wishlist.findIndex(item => item.id === book.id);
        if (index !== -1) {
          this.wishlist.splice(index, 1);
          book.inwishlist = false; 
        }
      },
      isBookInWishlist(book) {
        return this.wishlist.some(item => item.id === book.id);
      },
      getPageColor(pages) {
        if (pages >= 100) {
          return 'red';
        } else if (pages <= 50) {
          return 'orange';
        } else {
          return 'green';
        }
      },
      toggleWishlist() {
        this.isWishlistVisible = !this.isWishlistVisible;
      }
    }
  };
  </script>
  
  <style>
  .add-to-wishlist.disabled {
    background-color: red;
    cursor: not-allowed;
  }
  .green { color: green; }
  .orange { color: orange; }
  .red { color: red; }
  </style>
  