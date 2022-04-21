<template>
<!--  eslint-disable  -->
    <div>
        <!-- Checkout -->
        <div v-for="lesson in cart" :key="lesson.id" class="sum">
                    <div class="card border-primary w-20">
                        <div class=" card-body">
                            <div class="aligned">
                                <i :class="lesson.image"></i>
                                <span class="line">
                                    <br><br>
                                    <p>Subject: {{lesson.title}}</p>
                                    <p>Location: {{lesson.location}}</p>
                                    <p>Added availableSpace: {{lesson.availableSpace}}</p>
                                    <p>Price: {{lesson.price*lesson.availableSpace | currency}}</p>
                                </span>
                            </div>

                            <div class="text-center mt-2">
                                <button class=" btn btn-primary btn-sm w-50"
                                    v-on:click='removeFromCart(lesson)'>Remove</button>

                            </div>
                        </div>
                    </div>
                    <br>
                </div>
		        <div>
                    <h2>Checkout</h2>

                    <form @submit="checkForm">
                        <span>
                            <strong>Name:</strong>
                            <input id="name" type="text" v-model="name" v-on:keypress="checkForm" v-on:keyup="checkForm"
                                required />

                        </span>
                        <span>
                            <strong>Phone:</strong>
                            <input id="phone" type="number" v-model.number="phone" v-on:keypress="checkForm"
                                v-on:keyup="checkForm" required />

                        </span>
                        <span>
                            <button class="btn btn-primary btn-sm" type="submit" v-if="form" v-on:click="$parent.checkout = false"
                               >Checkout</button>

                            <button class="btn btn-primary btn-sm" v-else disabled="disabled">Checkout</button>

                        </span>
                    </form>
                </div>
        
    </div>
</template>

<script>
/* eslint-disable */

export default {
  name: "Checkout",
  props: {
    cart: {
      type: Array,
      required: true,
    },
  },
  data() {
    return {
      checkout: false,
      name: "",
      phone: "",
      successAlert: false,
      isValid: false,
    };
  },
  methods: {
    removeFromCart(lesson) {
      this.$emit('removeFromCart', lesson)
    },

    showCheckout() {
      this.showlesson = this.showlesson ? false : true;
    },

    canAddToCart(lesson) {
      return lesson.availableSpace > this.cartCount(lesson.id);
    },

    cartCount(id) {
      let count = 0;
      for (let i = 0; i < this.lessonCart.length; i++) {
        if (this.lessonCart[i] === id) {
          count++;
        }
      }
      return count;
    },

    checkoutSuccess() {
      alert("Order Submitted!");
    },

    async put(updateLess, id) {
      try {
        let response = await fetch(
          "https://cst-3145-cw.herokuapp.com/collection/Products/" + id,
          {
            method: "PUT",
            body: JSON.stringify(updateLess),
          }
        )
          .then((updateLess) => console.log(updateLess))
          .then(console.log("products collcts updated successfully"))
          .catch((err) => console.log(err));

        let newData = await response;
        return newData;
        console.log(newData);
      } catch (error) {
        console.log(error);
      }
    },

    checkout() {
      alert("Order Submitted!");

      let orders = {
        checkoutName: document.getElementById("name").value,
        checkoutPhone: document.getElementById("phone").value,
        cartProduct: this.cart,
      };

      fetch("https://cst-3145-cw.herokuapp.com/collection/Orders", {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
        },
        mode: "cors",
        cache: "no-store",
        body: JSON.stringify(orders),
      })
        .then((response) => response.json())
        .then((responseJSON) => {
          let updateLess = [];
          for (let element of this.cart) {
            updateLess.push({
              availableSpace: this.cart[element].availableSpace,
            });
            this.put(updateLess, this.cart[element]._id);
          }
          //console.log(this.cart);
        })
        .catch((error) => {
          console.log(error);
        });
    },

    checkForm() {
      let name = this.name;
      let phone = this.phone;
      this.form = name.match("^[a-zA-Z_ ]+$") && phone ? true : false;
    },
  },
};
</script>
