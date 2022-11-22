<script setup>
import Cards from "./components/Cards.vue";
</script>

<template>
  <button @click="reset" v-if="hideButton" @update:parent="fromChild = $event">
    Rematch
  </button>
  <Cards :key="randomkey" :hideButton="hideButton" @done="check($event)" />
</template>

<script>
export default {
  data() {
    return {
      hideButton: false,
      randomkey: 0
    };
  },
  methods: {
    check(e) {
      this.hideButton = e;
    },
    reset() {
      this.randomkey += 1;
      this.hideButton = !this.hideButton;
    }
  }
};
</script>
<style scoped>
header {
  line-height: 1.5;
}

.logo {
  display: block;
  margin: 0 auto 2rem;
}

@media (min-width: 1024px) {
  header {
    display: flex;
    place-items: center;
    padding-right: calc(var(--section-gap) / 2);
  }

  .logo {
    margin: 0 2rem 0 0;
  }

  header .wrapper {
    display: flex;
    place-items: flex-start;
    flex-wrap: wrap;
  }
}
</style>
