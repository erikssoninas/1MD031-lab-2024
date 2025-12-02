<template>
  <div class="menu-item">
    <h3>{{ burger.name }}</h3>
    <img :src="burger.image" />
    <ul class="content">
      <li v-for="item in burger.content" :key="item">{{ item }}</li>
    </ul>
    <p>{{ burger.kCal }} kCal </p>
    <p><button @click="removeBurger">-</button> {{ amountOrdered }} <button @click="addBurger">+</button></p>
  </div>
</template>



<script>

export default {
  name: 'OneBurger',
  props: {
    burger: Object
  },

  data: function () {
    return {
      amountOrdered: 0,
    }
  },
  methods: {
    removeBurger: function () {
      this.amountOrdered = Math.max(this.amountOrdered - 1, 0);
      this.$emit('orderedBurger', {
        name: this.burger.name,
        amount: this.amountOrdered
      }
      );
    },
    addBurger: function () {
      this.amountOrdered += 1
      this.$emit('orderedBurger', {
        name: this.burger.name,
        amount: this.amountOrdered
      }
      );
    }
  }

}

</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.content {
  font-weight: lighter;
  font-size: 12pt;
  color: rgb(177, 62, 62);
}

.menu-item {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.burger-image {
  width: 100%;
  height: auto;
}
</style>