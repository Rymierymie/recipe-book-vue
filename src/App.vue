<template>
  <div id="app">
    <navigation msg="Nav"/>
    <button @click="display('shop')">Shop</button>
    <button @click="display('search')">Search</button>
    <button @click="display('plan')">Plan</button>
    <div v-if="this.selected=='search'" class="component" id="search-component">
      <search />
    </div>  
    <div v-if="this.selected=='plan'" class="component" id="plan-component">  
      <mealplanner />
    </div>
    <div v-if="this.selected=='shop'" class="component" id="shop-component">
      <shop />
    </div>
  </div>
</template>

<script>
import Nav from './components/Nav.vue'
import Search from './components/Search.vue'
import Plan from './components/Plan.vue'
import Shop from './components/Shop.vue'

export default {
  name: 'app',
  data () {
    return {
      selected: 'shop'
    }
  },
  components: {
    navigation: Nav,
    search: Search,
    mealplanner: Plan,
    shop: Shop
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

.component {
  margin: 10px 10px;
  padding: 10px 10px;
  border: solid 1px black;
}

</style>
