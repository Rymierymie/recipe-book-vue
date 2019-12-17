<template>
  <div id="app">
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
    <button @click="clear_all_local()">Clear All Local</button>
  </div>
</template>

<script>
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
    search: Search,
    mealplanner: Plan,
    shop: Shop
  },
  methods: {
    display(elem){
       this.selected = elem;
       console.log(this.selected);
    },
    clear_all_local(){
      localStorage.clear();
      /* eventBus.$emit('local_cleared', true); */
      alert("Local Storage Cleared!")
    }

  }
}
</script>

<style>

#app {
  font-family: Arial, Helvetica, sans-serif;
}

.component {
  margin: 10px 10px;
  padding: 10px 10px;
  border: solid 1px rgba(0, 0, 0,0.1);
  border-radius: 5px;
}

</style>
