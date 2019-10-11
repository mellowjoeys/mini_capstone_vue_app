<template>
  <div class="home">
    <div>
      <h1>New Product</h1>
      <ul v-for="error in errors">{{ error }}</ul>

      <div>
        Name: <input type="text" v-model="newProductName">
      </div>
        
      <div>
        Price: <input type="text" v-model="newProductPrice">
      </div>
      
      <div>
        Description: <input type="text" v-model="newProductDescription">
      </div>
      
      <div>
        Image URL: <input type="text" v-model="newProductImageUrl">
      </div>


      <button v-on:click="createProduct()">Create</button>
    </div>

    <h1>All Products</h1>

    <div v-for="product in products">
      <h3>{{ product.name }}</h3>
      <img v-on:click="showProduct(product)" v-bind:src="product.image_url">

      <div v-if="product === currentProduct">
        <p>Price: {{ product.price }}</p>
        <p>Description: {{ product.description }}</p>
        <div class="edit-form-section">
          <h4>Edit Product</h4>

          <div>
            Name: <input type="text" v-model="product.name">
          </div>
            
          <div>
            Price: <input type="text" v-model="product.price">
          </div>
          
          <div>
            Description: <input type="text" v-model="product.description">
          </div>
          
          <div>
            Image URL: <input type="text" v-model="product.image_url">
          </div>

          <button v-on:click="updateProduct(product)">Update</button>
        </div><!-- end of .edit-form-section -->
        <div class="destroy-section">
          <button v-on:click="destroyProduct(product)">Destroy</button>
        </div> <!-- end of .destroy-section -->
      </div>
      
    </div>
  </div>
</template>

<style>
img {
  width: 250px;
}
</style>

<script>
var axios = require("axios");

export default {
  data: function() {
    return {
      products: [],
      errors: [],
      newProductName: "",
      newProductPrice: 0,
      newProductDescription: "",
      newProductImageUrl: "",
      currentProduct: {}
    };
  },
  created: function() {
    axios
      .get("/api/products")
      .then(response => {
        this.products = response.data;
      });
  },
  methods: {
    destroyProduct: function(productObject) {
      axios
        .delete("/api/products/" + productObject.id)
        .then(response => {
          var index = this.products.indexOf(productObject);
          this.products.splice(index, 1);
        });
    },

    updateProduct: function(productObject) {
      var clientParams = {
        name: productObject.name,
        price: productObject.price,
        description: productObject.description,
        image_url: productObject.image_url
      };

      axios
        .patch("/api/products/" + productObject.id, clientParams)
        .then(response => {
          console.log("Success", response.data);
        });
    },

    showProduct: function(productObject){
      if (this.currentProduct !== productObject) {
        this.currentProduct = productObject;
      } else {
        this.currentProduct = {};
      }
    },

    createProduct: function() {
      console.log("Create the product...");

      var clientParams = {
        name: this.newProductName,
        price: this.newProductPrice,
        description: this.newProductDescription,
        image_url: this.newProductImageUrl
      };

      axios
        .post("/api/products", clientParams)
        .then(response => {
          console.log("success", response.data);
          this.products.push(response.data); 
          this.errors = [];

          this.newProductName = "";
          this.newProductPrice = 0;
          this.newProductDescription = "";
          this.newProductImageUrl = "";
        })
        .catch(error => {
          this.errors = error.response.data.errors;
        });
    }
  }
};
</script>