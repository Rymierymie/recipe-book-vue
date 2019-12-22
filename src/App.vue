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
    <div>
      <p id="recentlyViewedList">Recently Viewed:
        <span v-for="(item, index) in recentlyViewed" v-bind:key="index" @click="viewRecentView(item)">&nbsp;{{ item }}<span v-if="recentlyViewed.length > 1">&nbsp;|&nbsp;</span></span>
      </p>
    </div>
    <button @click="clear_all_local()">Clear All Local</button>
  </div>
</template>

<script>
import Search from './components/Search.vue'
import Plan from './components/Plan.vue'
import Shop from './components/Shop.vue'

import { eventBus } from './event-bus';

export default {
  name: 'app',
  data () {
    return {
      selected: 'shop',
      recentlyViewed: []
    }
  },
  created (){
    console.log("App created")
          if (localStorage.recentlyViewed) {
          this.recentlyViewed = JSON.parse(localStorage.getItem('recentlyViewed'));
          } else {
            localStorage.setItem('recentlyViewed', JSON.stringify(this.recentlyViewed))
          }
          eventBus.$on('recentView', viewedItem => {
                let index = this.recentlyViewed.indexOf(viewedItem);
                if (index !== -1){
                  this.recentlyViewed.splice(index,1);
                }
                
                this.recentlyViewed.unshift(viewedItem);
                if (this.recentlyViewed.length > 4){
                  this.recentlyViewed.pop();
                }
                localStorage.setItem('recentlyViewed', JSON.stringify(this.recentlyViewed));
                });

  },
  computed: {
/*     viewedList: function (){
      console.log("words");
      return 1
    } */
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
    viewRecentView(item){
      console.log(item);
      this.display('search');
      eventBus.$emit('viewRecentClick', item);
    },
    clear_all_local(){
      localStorage.clear();
      /* eventBus.$emit('local_cleared', true); */
      alert("Local Storage Cleared!")
      location.reload();
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

#recentlyViewedList {
  font-size: 0.8rem;
}

</style>
