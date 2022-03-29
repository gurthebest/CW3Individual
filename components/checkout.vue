<template>
<div> 
<!-- checkout page code -->
        
            <button v-on:click='backButton'>
                <span  class="fas fa-arrow-left"></span> Back
            </button>
 
            <!-- Basket code to diplay products in basket-->
            <h2> My Basket </h2>
            <div class="contain">
                <div class="prod_box" v-for="(item, index) in cart" :key="index">
                    <p v-text="item.lessons.subject"> </p>
                    <img v-bind:src="item.lessons.image" height="50">
                    <p v-text="item.lessons.location"> </p>
                    <p>Price: {{item.lessons.price}}</p>
                    <button v-on:click="deleteFromCart(item)">
                    delete
                </button>
                </div>
            </div>

            <!--diplay checkout inputs for user-->
        <div id="checkout">
                <h2> Checkout</h2>

                <form action="https://gurthebest.github.io/lesson-app/" method="GET" onchange="return checkData()" name="signup">
                    <br /><br />Name:
                    <input name="NAME" type="text" id="NAME" v-model="OrderName" />
                    <br /><br /> Phone Number:
                    <input name="PHONE" type="text" id="PHONE" v-model="OrderPhone" />
                    <input @click="placeOrder()"  id="Button" type="submit" value="Place Order">
                </form>
            </div> 

</div>        

</template>