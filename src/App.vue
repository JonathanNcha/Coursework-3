<template>
<div>
   <header>
            <h1 v-text="sitename"></h1>

            <div>
                <button class=" btn btn-secondary btn-sm" v-on:click='showCheckout' v-if='cartItemCount > 0'><span
                        class="fas fa-cart-plus"> </span> Cart
                    <span class="badge">{{cartItemCount}}</span>
                </button>
                <button class="btn btn-secondary btn-sm" disabled="disabled" v-else><span class="fas fa-cart-plus">
                    </span> Cart
                    <span class="badge">{{cartItemCount}}</span>
                </button>

                <div class="input-group input-size">
                    <input placeholder="Search..." type="search" class="form-control border-primary" v-if='showlesson'
                        v-model="filter">
                </div>

                <button class="btn btn-primary btn-sm" v-on:click='showCheckout' v-if='!(showlesson)'> Back to
                    Lessons
                </button>
            </div>

        </header>
        <br>

        <main>
            <!-- shows lesson information -->
            <div v-if='showlesson'>
                <aside>
                    <div>
                        <div>Sort By</div>
                        <div>
                            <input id="subject" type="radio" name="sort" v-model="sortBy" value="title"
                                v-on:click="setSortBy" required checked>
                            <label for="subject">Subject</label>
                        </div>

                        <div>
                            <input id="location" type="radio" name="sort" v-model="sortBy" value="location"
                                v-on:click="setSortBy">
                            <label for="location">Location</label>
                        </div>

                        <div>
                            <input id="price" type="radio" name="sort" v-model="sortBy" value="price"
                                v-on:click="setSortBy">
                            <label for="price">Price</label>
                        </div>

                        <div>
                            <input id="availability" type="radio" name="sort" v-model="sortBy" value="availableSpace"
                                v-on:click="setSortBy">
                            <label for="availability">Availability</label>
                        </div>
                        <br>
                        <div>
                            <input id="asc" type="radio" name="order" v-model="sortDirection" value="asc"
                                v-on:click="setSortDirection">
                            <label for="asc">Ascending</label>
                        </div>

                        <div>
                            <input id="desc" type="radio" name="order" v-model="sortDirection" value="desc"
                                v-on:click="setSortDirection">
                            <label for="desc">Descending</label>
                        </div>
                    </div>
                </aside>
                <div v-for="lesson in sortedLessons" :key="lesson.id" class="sum">
                    <div class="card border-primary w-20">
                        <div class=" card-body">
                            <div class="aligned">
                                <i :class="lesson.image"></i>
                                <span class="line">
                                    <br><br>
                                    <p>Subject: {{lesson.title}}</p>
                                    <p>Location: {{lesson.location}}</p>
                                    <p>Price: {{lesson.price | currency}}</p>
                                    <p>availableSpace: {{lesson.availableSpace-cartCount(lesson.id)}}</p>
                                </span>
                            </div>
                            <Lessons :sortedLessons="sortedLessons" @addCart="addToCart"/>
                            <div class="text-center mt-2">
                                <button class=" btn btn-primary btn-sm w-50" v-on:click='addToCart(lesson)'
                                    v-if='flag'>Add
                                    to
                                    cart</button>
                                <button class="btn btn-primary btn-sm w-50" disabled="disabled" v-else>Add to
                                    cart</button>
                            </div>
                        </div>
                    </div>
                    <br>
                </div>
                <div class="text-center noSearchMessage" v-if="sortedLessons.length===0">
                    <h2> No Match Found</h2>
                </div>

            </div>

            <div v-else>
                <h2>Cart</h2>
                <Checkout v-if="checkout" :cart="cart" @removeFromCart="removeFromCart"/>
            </div>

        </main>
    </div>

</template>

    <script>
    import Lessons from './components/Lessons.vue'
import Checkout from './components/Checkout'
export default {
	/* eslint-disable */ 
	name: 'App',
	components: {
		Lessons,
		Checkout
	},
  	data() {
		return{
			sitename: '',
                lesson: [],
                cart: [],
                lessonCart: [],
                showlesson: true,
                name: '',
                phone: '',
                form: false,
                sortBy: 'title',
                sortDirection: 'asc',
                filter: '',
                flag: 'true'
		}
	},
	mounted() {
		this.getLessons();
	},

    
                methods: {
                    getLessons(){
                   fetch('https://cst-3145-cw.herokuapp.com/collection/Products')
			.then(response => response.json())
			.then(data => {
				this.lesson = data;
			});

},
                setSortBy() {
                    this.sortBy = '';
                },

                setSortDirection() {
                    this.sortDirection = '';
                },

                addToCart(lesson) {
                    var lessonTitle = lesson.title;
                    var lessonLocation = lesson.location;
                    var lessonPrice = lesson.price;
                    var lessonImage = lesson.image;
                    var addedavailableSpace = 1;
                    for (var index in this.cart) {
                        var cartItem = this.cart[index];
                        // if (lesson.id === cartItem.id) {
                        //     addedavailableSpace = parseInt(cartItem.availableSpace) + 1;
                        //     itemInCart = this.cart.indexOf(cartItem);
                        //     this.cart.splice(itemInCart, 1);
                        // }
                    }
                    var itemToAdd = {
                        title: lessonTitle,
                        location: lessonLocation,
                        price: lessonPrice,
                        image: lessonImage,
                        availableSpace: addedavailableSpace,
                        id: lesson.id
                    }
                    this.cart.push(itemToAdd);
                    this.lessonCart.push(lesson.id);
                },

                removeFromCart(lesson) {
                    lesson.availableSpace--;
                    if (lesson.availableSpace <= 0) {
                        var i = this.cart.indexOf(lesson);
                        this.cart.splice(i, 1);
                    }
                    this.lessonCart.pop(lesson.id);
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
                    alert('Order Submitted!')
                },


                async put(updateLess, id) {
                    try {
                        let response = await fetch('https://cst-3145-cw.herokuapp.com/collection/Products/' + id, {
                            method: 'PUT',
                            body: JSON.stringify(updateLess)
                        }).then(updateLess => console.log(updateLess)).then(console.log("products collcts updated successfully"))
                            .catch(err => console.log(err));;

                        let newData = await response;
                        return newData;
                        console.log(newData)
                    } catch (error) {
                        console.log(error);
                    }
                },

                checkout() {

                alert('Order Submitted!');

                    let orders = {
                        checkoutName: document.getElementById("name").value,
                        checkoutPhone: document.getElementById("phone").value,
                        cartProduct: this.cart,
                    }

                    fetch('https://cst-3145-cw.herokuapp.com/collection/Orders', {
                        method: 'POST',
                        headers: {
                            'Content-Type': 'application/json',
                        },
                        mode: "cors",
                        cache: "no-store",
                        body: JSON.stringify(orders),
                    })
                        .then(response => response.json())
                        .then(responseJSON => {
                            let updateLess = []
                            for (let element of this.cart) {
                                updateLess.push({ availableSpace: this.cart[element].availableSpace });
                                this.put(updateLess, this.cart[element]._id)
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
                    this.form = (name.match("^[a-zA-Z_ ]+$") && phone) ? true : false;
                },

                searchLessons(lesson, value, title, location) {
                    return lesson.filter(function (item) {
                        return item[title].toLowerCase().includes(value) || item[location].toLowerCase().includes(value)
                    })

                },

            },

            created: function () {

                fetch('https://cst-3145-cw.herokuapp.com/collection/Products').then(
                    function (response) {
                        response.json().then(
                            function (json) {
                                webstore.lesson = json;
                            });
                    })
            },

getLessons(){
    fetch('https://cst-3145-cw.herokuapp.com/collection/Products').then(
                    function (response) {
                        response.json().then(
                            function (json) {
                                webstore.lesson = json;
                            });
                    })

},
            computed: {
                sortedLessons() {
                    if (this.filter != 0)
                        return this.searchLessons(this.lesson, this.filter, 'title', 'location')

                    return this.lesson.sort((a, b) => {
                        let modifier = 1;
                        if (this.sortDirection === 'desc')
                            modifier = -1;
                        if (a[this.sortBy] < b[this.sortBy])
                            return -1 * modifier;
                        if (a[this.sortBy] > b[this.sortBy])
                            return 1 * modifier;
                        return 0;
                    });

                },

                cartItemCount() {
                    return this.lessonCart.length;
                },
            },

            filters: {
                currency(price) {
                    return "$" + price;
                }
            }
        };

    </script>

     <!-- internal stylesheet -->
        <style>
          #app {
              margin: 4%;
          }
  
          .aligned {
              display: flex;
              align-items: center;
          }
  
          .card {
              border-radius: 8%;
          }
  
          span {
              padding-left: 20px;
          }
  
          .w-20 {
              width: auto;
              height: auto;
          }
  
          .w-50 {
              width: 50%;
          }
  
          .line {
              line-height: 50%;
              ;
          }
  
          .sum {
              display: inline-block;
              padding: 15px;
              margin: 5px;
          }
  
          aside {
              float: left;
              height: 800px;
          }
  
          .input-size {
              width: 50%;
              float: right;
          }
      </style>