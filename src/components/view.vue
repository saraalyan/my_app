<template>
    <div class="main">
            <nav>
                <ul class="nav nav-tabs">
                    <li class="nav-item">
                        <a class="nav-link" @click.prevent="showproducts" :class="{ active: activeTab === 'products' }">Products</a>
                    </li>
                    <li class="nav-item">
                        <a class="nav-link" @click.prevent="showWishlist" :class="{ active: activeTab === 'wishlist' }">Wishlist</a>
                    </li>
                </ul>
            </nav>
            
            <div >
                <keep-alive>
                <component :is="targetcomponent" />
                </keep-alive>
            </div>
         
        
        </div>
</template>

<script>
import products from "./ProductComponent.vue";
import wishlist from "./navcomponnent.vue";

export default {
    data() {
        return {
            activeTab: 'products',
            targetcomponent: 'products'
            
        };
    },
    methods: {
        showproducts() {
            this.activeTab = 'products';
            this.targetcomponent = 'products';
        },
        showWishlist() {
            this.activeTab = 'wishlist';
            this.targetcomponent = 'wishlist';
        }
    },
    components: {
        products,
        wishlist
    }
};
</script>

<style>
</style>