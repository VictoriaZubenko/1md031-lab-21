<template>

    <div style="float:left" id="img1">
      <h3>{{burger.name}}</h3>
      <img v-bind:src="burger.image" style="margin-right: 15px; height: 200px; width: 200px;" >
      <ul>
        <li>{{burger.kCal}} kCal </li>
        <li v-if="burger.lactose"> Contains <span class="hamburgartext"> lactose</span></li>
        <li v-if="burger.gluten"> Contains <span class="hamburgartext"> gluten</span></li>
        <li v-else> <span class="hamburgartext">Gluten free </span></li>
      </ul>
      <div>
      <button v-on:click="addBurger">
        +
      </button>
      <button v-on:click="removeBurger">
        -
      </button>
      <p>amount: {{ amountOrdered }}</p>
  </div>
    </div>
</template>

<script>
export default {
  name: 'Burger',
  props: {
    burger: Object
  },
  data: function () {
    return {
      amountOrdered: 0,
    }
  },
  methods: {
    addBurger: function(){
      this.amountOrdered += 1;
      this.$emit('orderedBurger', { name:this.burger.name,
                                    amount: this.amountOrdered }
      );
    },
    removeBurger: function(){
      if (this.amountOrdered >0) {
        this.amountOrdered -= 1
      }
      return this.amountOrdered

    }

  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>
