<template>
    <div>
         <div class="title">Items</div>
                <hr>
        <div v-if="items.length !== 0" class="item-container">
           
            <div class="item" v-for="(item, index) in items" v-bind:item="item" v-bind:index="index"
                v-bind:key="item?.id">{{ item?.name }}<br /><br /> <button @click="addToCart(item?.id, item?.name)">Add to Cart</button></div>
        </div>
            <div v-if="cart.length !== 0" class="cart-container">
                 <div>Cart</div>
        <hr>
                <div class="cart" v-for="(cartItem, index) in cart" v-bind:cartItem="cartItem" v-bind:index="index"
                    v-bind:key="cartItem?.id"><div class="cart-contents">{{ `name: ${cartItem?.data.itemName}` }}</div><div class="cart-contents">{{ ` qty: ${cartItem?.data.qty}` }} <button class="remove-from-cart" @click="removeFromCart(cartItem)">Remove</button></div></div>
            </div>
        
    </div>
</template>

<script>

import axios from 'axios';

export default {
    name: 'ItemComponent',
    props: {
        items: []
    },
      data() {
        return {
            cart: [],
            error: '',
            url: 'http://localhost:5000/api/cart'
        }
    },
    //load the card
      async created() {
        axios
            .get(this.url)
            .then((response) => {
                const data = response.data;
                this.cart = data;
            })
            .catch((err) => {
                console.error(err);
                throw err;
            });
    },
    methods: {
            addToCart(itemId, itemName) {
            //we are defining a default object to send over....
            let cartItemToAdd = {
                exists: false,
                data: {
                    id: itemId,
                    itemName,
                    qty: 1,
                    redisIndex: 0
                }
            }
            if(this.cart.length) {
                const foundCartItem = this.cart.find((c) => c.data.id === itemId)
                if(foundCartItem?.data.id) {
                    //...and changing the value to the found contents if the item already exists
                      cartItemToAdd = {
                    exists: true,
                    data: {
                        id: itemId,
                        itemName,
                        qty: Number(foundCartItem.data.qty),
                        redisIndex: foundCartItem.data.redisIndex
                    }
                }
            }
                }
            axios
                .post(`${this.url}/addtocart`, cartItemToAdd)
                .then(() => {
                          axios
                        .get(this.url)
                        .then((response) => {
                            const data = response.data;
                            this.cart = data;
                        })
                        .catch((err) => {
                            console.error(err);
                            throw err;
                        });
                })
                .catch((err) => {
                    console.error(err);
                    this.error = err.message
                    throw err;
                });
        },
        removeFromCart(cartItem) {
            //pass the cart item directly the server
            axios
                .put(`${this.url}/removefromcart`, cartItem)
                .then(() => {
                    axios
                        .get(this.url)
                        .then((response) => {
                            const data = response.data;
                            this.cart = data;
                        })
                        .catch((err) => {
                            console.error(err);
                            throw err;
                        });
                })
                .catch((err) => {
                    console.error(err);
                    this.error = err.message
                    throw err;
                });
        }
    }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

.remove-from-cart {
    margin-left: 30%;
}

.title {
    font-size: 30px;
    padding: 14px;
}

.item-container {
    display: flex;
}
    .item {
        flex: 1;
        width: 30%;
        font-size: 20px;
        padding: 20px;
        box-shadow: 0px 4px 4px 2px rgba(0, 0, 0, 0.25);
        margin: 20px;
    }

    .cart {
         width: 90%;
        font-size: 20px;
        text-align: left;
        display: flex;
        margin-left: 10%;
    }
    .cart-contents {
        flex: 1;
        width: 30%;
    }
    .cart-container {
        position: fixed;
        bottom: 0;
        right: 0;
        margin-bottom: 10px;
        margin-right: 10px;
        width: 30%;
         box-shadow: 0px 4px 4px 4px rgba(0, 0, 0, 0.25);
         border-radius: 10px;
    }
</style>