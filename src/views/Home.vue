<template>



  <head>
    <link rel="stylesheet" type="text/css" href="style.css">
    <title>Burgare</title>
    <meta charset="utf-8"/>
  </head>
  <main>

    <section id="rubrik">
      <img src="https://res.cloudinary.com/tf-lab/image/upload/w_1200,h_674,c_fill,g_auto:subject,q_auto,f_auto/restaurant/73d6d3da-fbae-4b4f-af5b-4b14c68bf3ee/70d4ae42-9bbe-4d46-b3bd-8ba4e7ed114c.jpg" id="bild" style="width: 1139px;">
      <div class="rubrik1">
        <h1>Välkommen till BurgerOnline</h1>
      </div>


    </section>

    <section id="burgerinfo" >
      <header>
        <h2>Select burgers </h2>
      </header>
      <p> This is where you execute your burger!!!! </p>

      <div class="wrapper">
        <div>
          <Burger v-for="burger in burgers"
                  v-bind:burger="burger"
                  v-bind:key="burger.name"
                  v-on:orderedBurger="addToOrder($event)"/>

        </div>
      </div>

    </section>

    <section id="contact" >
      <div style="clear:left">
        <h2>Customer information </h2>
        <p>This is where you provide necessary information</p>
      </div>
      <h2>Delivery infromation: </h2>
      <form>
        <p>
          <label for="firstname">Full name</label><br>
          <input type="text" id="firstname" v-model="fn" required="required" placeholder="First -and Last name">
        </p>
        <p>
          <label for="email">E-mail</label><br>
          <input type="email" id="email" v-model="em" required="required" placeholder="E-mail address">
        </p>
        <!---<p>
          <label for="street">Street</label><br>
          <input type="number" id="street" v-model="st" required="required" placeholder="Street name">
        </p>
        <p>
          <label for="house">House</label><br>
          <input type="number" id="house" v-model="hn" required="required" placeholder="House number">
        </p>--->

        <label for="payment">Payment options</label><br>
          <select id="payment" v-model="pm"><br>
            <option>Swish</option>
            <option>Credit card</option>
            <option>Bank transaction</option>
            <option>Klarna</option>
          </select>


        <br>

        <p>Gender</p>

        <input type="radio" id="male" v-model="gender" value="male">
        <label for="male">Male</label><br>

        <input type="radio" id="female" v-model="gender" value="female">
        <label for="female">Female</label><br>

        <input type="radio" id="not" v-model="gender" value="not">
        <label for="not">Do not wish to provide</label><br>


        <br>

      </form>

      <div class="scrollish">
        <div id="map" v-on:click="setLocation" >
          <div v-bind:style="{left:location.x + 'px', top: location.y + 'px'}">
            T
          </div>
        </div>
      </div>

        <button v-on:click="markDone" class="button">
          <img src="https://upload.wikimedia.org/wikipedia/commons/thumb/8/8f/Checkmark.svg/900px-Checkmark.svg.png" style=" height: 15px; width: 15px;">
          Place order
        </button>

    </section>



  </main>
  <footer>
    <hr> &copy; 2018 Hypothetical Burgers Inc
  </footer>
</template>

<script>
import Burger from '../components/Burger.vue'
import io from 'socket.io-client'
import menu from '../assets/menu.json'

const socket = io();

/*function MenuItem(name, kCal, lactose, gluten, image) {
  this.name = name; // The *this* keyword refers to the object itself
  this.kCal = kCal;
  this.lactose =lactose;
  this.gluten =gluten;
  this.image = image;
  }*/

 let arrayofburgers = menu;

/*[new MenuItem('Korean BBQ Burger', 850, false, true, 'img/maxbbq.jpg'),
   new MenuItem('Salad Wrap Burger', 2,true, false, 'img/Wrapburger.jpg')
 ,new MenuItem('Crispy No Chicken', 2000, false, true, 'img/chicken.jpg')]*/



export default {
  name: 'Home',
  components: {
    Burger
  },
  data: function () {
    return {
      burgers: arrayofburgers,
      gender:'',
      pm:'',
      hn:'',
      st:'',
      em:'',
      fn:'',
      orderedBurgers:{},
      location:{x:0, y:0}
    }
  },
  methods: {
    getOrderNumber: function () {
      return Math.floor(Math.random()*100000);
    },

    setLocation: function (event) {
      var offset = {x: event.currentTarget.getBoundingClientRect().left,
        y: event.currentTarget.getBoundingClientRect().top};
        this.location = {
          x: event.clientX - 10 - offset.x,
          y: event.clientY - 10 - offset.y
        }
      },



    /*addOrder: function (event) {
      var offset = {x: event.currentTarget.getBoundingClientRect().left,
                    y: event.currentTarget.getBoundingClientRect().top};
      socket.emit("addOrder", { orderId: this.getOrderNumber(),
                                details: { x: event.clientX - 10 - offset.x,
                                           y: event.clientY - 10 - offset.y },
                                orderItems: ["Beans", "Curry"]
                              }
                 );
    },*/

      markDone: function() {
        console.log(this.gender, this.pm, this.hn,this.st,this.em,this.fn, this.orderedBurgers)
        socket.emit("addOrder", {
          orderId: this.getOrderNumber(),
          details: {
            gender: this.gender,
            pm: this.pm,
            hn: this.hn,
            em: this.em,
            fn: this.fn,
            x: this.location.x,
            y: this.location.y
          },
          orderItems: [this.orderedBurgers]
        });
      },
    addToOrder: function (event) {
      this.orderedBurgers[event.name] = event.amount;
    }


  }
}
</script>

<style>
  #map {
    /*width: 1920px;
    height: 1078px;
    background: url("/img/polacks.jpg");*/
    position: relative;
    width:20px;
    height:20px;
    background: url(/img/polacks.jpg);
    background-repeat: no-repeat;
    width:1920px;
    height: 1078px;
    cursor: crosshair;

  }

  #map div {
    position: absolute;
    background: black;
    color: white;
    border-radius: 10px;
    width:20px;
    height:20px;
    text-align: center;
  }
  .scrollish{
    overflow:scroll;
    width: 700px;
    height:400px;
  }
  body {
    font-family: Courier New;
  }
  .hamburgartext {
    color: #ff2600;
    font-weight:bold;
  }

  #burgerinfo {
    background-color: black;
    color: white;
    margin: 20px;
    border: 2px dashed #ff0000;
  }


  #contact {
    background-color: #eac3e6;
    margin: 20px;
    border: 2px dashed #ff0000;

  }
  #rubrik {
    margin: 20px;
    height: 250px;
    overflow:hidden;

  }

  .rubrik1{
    position:absolute;
    padding:20px;
    margin-top:-650px;
  }
  .button:hover {
    background-color: #97a8d3;
    cursor:pointer;

  }
  .button {
    margin: 15px;
  }

  p {
    margin: 10px;
  }

  h2 {
    margin: 10px;
  }


  #bild {
    opacity: 0.5;
    width:100%;
    height:auto;
  }

  .wrapper{
    display:grid;
    grid-gap:10px;
    grid-template-colums: 33% 33% 33%;
    grid-auto-rows: 100%;

  }

  section div {
    paddning:20px;
    margin:30px;
  }

  section  {
    paddning:20px;
    margin:20px;
  }

</style>
