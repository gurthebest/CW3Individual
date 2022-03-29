<template> 

   <div id="app">
   <head>
     <h1>{{sitename}}</h1>

     <title>Afterschool Activity</title>

   </head>
       <header>
                <button v-on:click='Checkoutview' v-if="this.cart.length">
                {{this.cart.length}}
                <span class="fas fa-shopping-cart"></span> Checkout
            </button>
       </header>
<main> 
<div v-if="presentView"> 

        <!-- Sorting Options -->
            <div id="sort">
                <p>Sort by</p>
                <div v-for="(selectedValue, key) in sorting" :key=" 'A' + key">
                
                    <input type="radio" id="key" name="key" value="selectedValue" v-on:click='sortBy(selectedValue)'>
                    <label for="key"> {{key}}</label><br>
                </div>

                <p>
                    <strong>Order:</strong>
                    <select v-model="orderAscending">
                    <option disabled value="">Order By</option>
                    <option v-for="(selectedValue, key) in userOrder" v-on:change='ChangeOrder' v-bind:value="selectedValue"  :key=" 'A' + key">
                        {{selectedValue}}
                        </option>
                </select>
                </p>
            </div>

            <!-- Searching Options -->
            <div id="search">
                <input placeholder="Search..." type="text" v-model="search">  {{lessonsFilter}}
            </div>
 

      <lesson  :lessons="lessons"  @addLesson='addToCart'/>
  </div>
 
  <div v-else > 
    <checkout  @Checkoutview='Checkoutview' :cart="cart" @deleteFromCart='deleteFromCart' @placeOrder='placeOrder' />
  </div>
   
</main>
 

  </div>
</template>

<script>
/* eslint-disable */
import checkout from './components/checkout.vue'
import lesson from './components/lesson.vue'
export default {
  name: 'App',
  components: { checkout, lesson },
  data(){
    return{
      sitename: 'AfterSchoolBooking',
      cart: [],
      presentView: true,
      lessons:[],
     orderAscending: '',
     search: '',
     sorting: {Price: 'price',
                Subject: 'subject',
                Location: 'location',
                Availability: 'availableInventory'},
    userOrder: {
        a: 'Ascending',
        b: 'Descending'
            },
    }
  },
  methods:{
   Checkoutview() {
                this.presentView = this.presentView ? false : true;
            },
    placeOrder() {
                for (let s = 0; s < this.lessons.length; s++) {
                    let lessonID = this.lessons[s].id
                    let spaces = this.cartCount(lessonID)
                    if (spaces > 0) {
                      
                        this.cartLessonsID.push({
                            lessonID
                        })
                        this.cartLessonsCounter.push({
                            spaces
                        })
                    }
                }
            fetch('https://cst31452022.herokuapp.com/collection/orderinfo', {
                    method: 'POST',
                    headers: {
                        'Accept': 'application/json',
                        'Content-Type': 'application/json'
                    },
                    body: JSON.stringify({
                       "LessonID":  this.cartLessonsID,
                        "Name": this.OrderName,
                        "Phone": this.OrderPhone,
                        "Spaces": this.cartLessonsCounter

            }),
                })
                for (let i = 0; i < this.cartLessonsID.length; i++) { //Loop for every lesson that is sent to the database
                    for (let s = 0; s < this.lessons.length; s++) { //Loop for every lesson available
                        let lessonID = this.lessons[s].id
                        if (lessonID === this.cartLessonsID[i].lessonID) {
                            var availableSpace = this.lessons[s].availableInventory // - this.cartLessonsCounter[i].counter
                            var tmpLessonID = this.cartLessonsID[i].lessonID
                            console.log(tmpLessonID + 'TEMP ID')
                            fetch('https://cst31452022.herokuapp.com/update/lessons/' + lessonID, {
                                method: 'PUT',
                                headers: {
                                    'Accept': 'application/json',
                                    'Content-Type': 'application/json'
                                },
                                body: JSON.stringify({
                                    "availableInventory": availableSpace
                                }),
                            })
                        }
                    }
                }
                alert('Order Submitted Successfully!')
                document.location.reload(true)
            },
            addToCart(lessons){
              let tmpLessonID = lessons.id           
                this.cart.push({
                    cartId: (this.cart.length + 1),
                    lessons,
                    tmpLessonID
                })
            },
            deleteFromCart: function(lesson) {
                this.cart = this.cart.filter(item => item.cartId != lesson.cartId)
                function ifCan(item) {
                    if (item.id == lesson.lessons.id) {
                        console.log("adding " + item.id)
                        item.availableInventory += 1
                    }
                }
                             
                this.lessons.find(ifCan)
       }
  }, 
  computed: {
              ChangeOrder() {
                if (this.orderAscending === "Ascending") {
           
                     this.orderAscending = "Ascending"
           
                     return this.lessons.reverse();
                } else if (this.orderAscending === "Descending") {
                   
                    this.orderAscending = "Descending"
                  
                    return this.lessons.reverse();
                } else {
                
                    return this.lessons;
                }
            },
            lessonsFilter() {
                var localApp = this;
 
                if (!this.search) {
                   
                   
                    fetch('https://cst31452022.herokuapp.com/collection/lessons', {
                        method: 'GET'
                    })
                        .then(function(response) {
                            if (response.ok) {
                                console.log('Successfully fetched the Data')
                            } else {
                                console.log('Error fetching data')
                            }
                            return response.json();
                        })
                        .then(function(json) {
                            // use the json
                            console.log(json)
                            localApp.lessons = json;
                        }).catch(function(error) {
                        //Handle error
                        console.log(error);
                    });
                } else {
           
                    fetch('https://cst31452022.herokuapp.com/Search/lessons/?search=' + this.search + '', {
                        method: 'GET'
                    })
                        .then(function(response) {
                            if (response.ok) {
                                console.log('Success')
                            } else {
                                console.log('Error fetching data')
                            }
                            return response.json();
                        })
                        .then(function(json) {
                            console.log(json)
                            localApp.lessons = json;
                        })
                }
 
 
  
 }
       
  },
        created: function() { 
                
                this.lessonsFilter;
        }
  
}
</script>
 
 