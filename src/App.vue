<template>
  <div id="app">
    <navigation msg="Recipe Book App"/>
    <shop2 />
    <div>
      <button @click="display('search')">Search</button>
      <button @click="display('mealplanner')">Plan</button>
      <button @click="display('shop')">Shop</button>
        <keep-alive>
        <component v-bind:is="selected"></component>
        </keep-alive>
    </div>
  </div>
</template>

<script>
import Nav from './components/Nav.vue'
import Search from './components/Search.vue'
import Plan from './components/Plan.vue'
import Shop from './components/Shop.vue'
import Shop_2 from './components/Shop_2.vue'

export default {
  name: 'app',
  data () {
    return {
      selected: null
    }
  },
  components: {
    navigation: Nav,
    search: Search,
    mealplanner: Plan,
    shop: Shop,
    shop2: Shop_2
  },
  methods: {
    display(elem){
       this.selected = elem;
       console.log(this.selected);
    }

  }
}
</script>

<style>

</style>
