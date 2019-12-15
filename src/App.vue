<template>
  <div id="app">
    <navigation msg="Nav"/>
    <div class="component">
      <search />
    </div>  
    <div class="component">  
      <mealplanner />
    </div>
    <div class="component">
      <shop2 />
    </div>
  </div>
</template>

<script>
import Nav from './components/Nav.vue'
import Search from './components/Search.vue'
import Plan from './components/Plan.vue'
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

.component {
  margin: 20px 20px;
}

</style>
