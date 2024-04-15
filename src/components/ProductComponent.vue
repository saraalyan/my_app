<template>
    <div id="product-store">
      <!-- Banner -->
      <div class="d-flex align-items-baseline justify-content-between bg-dark text-light p-2 m-2">
        <a href="#" style="color:yellow;text-decoration:none;" @click.prevent="toggleView">{{ isProductsView ? 'Show Cart' : 'Products' }}</a>
        <div class="align-items-baseline d-flex">
          <p class="mx-1">[{{ cart.items.length }}] <template v-if="cart.items.length === 1">item</template>  <template v-else>items</template> with total [ {{ formateCurrency(getTotalPrice()) }} ]</p>
        </div>
      </div><!-- end banner-->
  
      <!-- Content -->
      <div v-if="isProductsView">
        <!-- Products -->
        <div class="row align-items-baseline justify-content-between bg-dark p-2 m-2">
          <div class="card mx-auto my-1" v-for="product in products" :key="product.id" style="width:26rem;margin:0.2rem;">
            <img :src="product.image" :title="product.name"/>
            <h4 class="my-1 text-center">{{product.name}}</h4>
            <p style="text-align:justify;">{{product.description}}</p>
            <div class="card-footer">
              <div class="d-flex align-items-baseline justify-content-between">
                <p :class="[product.instock >= 5 ? 'more' : '', product.instock < 5 ? 'less' : '', product.instock == 0 ? 'none' : '']">{{product.instock}} in Stock </p>
                <button class="btn btn-primary" :disabled="product.instock == 0" @click="addToCart(product)">AddToCart</button>
              </div>
            </div>
          </div>
        </div><!-- end of products-->
      </div>
      <div v-else>
        <!-- Cart -->
        <div class="d-flex align-items-baseline justify-content-between p-2 m-2">
          <h4 class="w-100 text-center text-danger" v-if="cart.items.length === 0">Sorry, Add Products!!!</h4>
          <table v-else class="table table-bordered table-striped text-center">
            <!-- Cart table content -->
            <thead>
              <tr>
                <th>ID</th>
                <th>Name</th>
                <th>Price</th>
                <th>Quantity</th>
                <th>Actions</th>
                <th>TotalPrice</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="item in cart.items" :key="item.product.id">
                <td>{{ item.product.id }}</td>
                <td>{{ item.product.name }}</td>
                <td>{{ formateCurrency(item.product.price) }}</td>
                <td>{{ item.quantity }}</td>
                <td>
                  <button class="btn btn-success h5" @click="increaseQuantity(item)" :disabled="item.product.instock == 0">+</button>&nbsp;&nbsp; |&nbsp;&nbsp;
                  <button class="btn btn-danger h5" @click="decreaseQuantity(item)">-</button>
                </td>
                <td>{{ formateCurrency(item.quantity * item.product.price) }}</td>
              </tr>
            </tbody>
            <tfoot>
              <tr>
                <th colspan="4">TotalPrice</th>
                <th colspan="2">{{ formateCurrency(getTotalPrice()) }}</th>
              </tr>
              <tr>
                <th colspan="4">TotalTaxes</th>
                <th colspan="2">{{ formateCurrency(getTotalPrice() * 0.1) }}</th>
              </tr>
              <tr>
                <th colspan="4">GrandTotal</th>
                <th colspan="2">{{ formateCurrency(getTotalPrice() + getTotalPrice() * 0.1) }}</th>
              </tr>
            </tfoot>
          </table>
        </div><!-- end of cart-->
      </div>
    </div><!-- end of mainapp-->
  </template>
  
  <script>
import products from "./products.js";

export default {

    data() {
      return {
        products: products, 
        isProductsView: true,
        cart: { items: [] }
      };
    },
    methods: {
      formateCurrency(value) {
        return Intl.NumberFormat("ar-SA", {
          style: "currency",
          currency: "SAR",
          minimumFractionDigits: 0
        }).format(value);
      },
      addToCart(product) {
        if (this.cart.items.some(iitem => iitem.product.id == product.id)) {
          // exist
          this.cart.items.find(iitem => iitem.product.id == product.id).quantity++;
        } else {
          // new product
          this.cart.items.push({ product: product, quantity: 1 });
        }
        product.instock--;
      },
      increaseQuantity(item) {
        item.quantity++;
        item.product.instock--;
      },
      decreaseQuantity(item) {
        item.quantity--;
        if (item.quantity == 0) {
          this.cart.items.splice(this.cart.items.findIndex(iitem => iitem.product.id == item.product.id), 1);
        }
        item.product.instock++;
      },
      getTotalPrice() {
        let _total = 0;
        this.cart.items.forEach(element => {
          _total += element.product.price * element.quantity;
        });
        return _total;
      },
      toggleView() {
        this.isProductsView = !this.isProductsView;
      }
    }
  };
  </script>
  
  <style>
  .more { color: green; }
  .less { color: orange; }
  .none { color: red; }
  .more, .less, .none { font-weight: bold; }
  </style>
  