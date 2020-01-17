<template>
  <div id="app">
    <link href="https://fonts.googleapis.com/css?family=Poppins:200,400,600&display=swap" rel="stylesheet">
  <div id="nav">  
    <img src="./assets/icons/utensils.png" class="nav-icon" @click="display('search')" />
    <img src="./assets/icons/calendar.png" class="nav-icon" @click="display('plan')" />
    <img src="./assets/icons/tasks.png" class="nav-icon" @click="display('shop')" />
  </div>  
  <!-- <div class="divider">
  </div> -->
  <div style="padding-left:15px;" v-if="selected === 'search'">
    <p v-if="this.recentlyViewed.length !== 0" id="recentlyViewedList">Recent:&nbsp;
      <a href="#recipe-card"><span class="recentlyViewedItem" v-for="(item, index) in recentlyViewed" v-bind:key="index" @click="viewRecentView(item)">{{ item }}<img class="recentlyViewedDivide" src="./assets/icons/divider.png" v-if="recentlyViewed.length > 1"></span></a>
    </p>
  </div>
    <div v-if="this.selected=='search'" class="component" id="search-component">
      <search />
    </div>  
    <div v-if="this.selected=='plan'" class="component" id="plan-component">  
      <mealplanner />
    </div>
    <div v-show="this.selected=='shop'" class="component" id="shop-component">
      <shop />
    </div>
<!--     <div style="padding: 20px 0px 20px 0px">
    <button @click="clear_all_local()">Clear All Local</button>
    </div> -->
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
      selected: 'search',
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
                if (this.recentlyViewed.length > 3){
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

  },
  watch: {
    recentlyViewed: function(){
      console.log("this changed");
      if (this.recentlyViewed.length > 1){
        let items = document.getElementsByClassName("recentlyViewedDivide");
        let index = items.length - 1;
        console.log(items);
        console.log(index);
        items[index].style.display = 'none'
        
      }
    }
  }
}
</script>

<style>


html {
  scroll-behavior: smooth;
}

#app {
  font-family: 'Poppins', sans-serif;
}

body {
  margin: 0px 0px;
  color: #333333;
  background-color: rgba(185, 217, 195, 0.1);
}

.componentDiv {
  margin: 5px 15px;
}

.component {
/*   margin: 10px 10px;
  padding: 10px 10px;
  border: solid 1px rgba(0, 0, 0,0.1);
  border-radius: 5px; */
}

.card {
    margin: 20px 0px;
    padding: 10px 10px;
    border: solid 1px rgba(0, 0, 0,0.1);
    border-radius: 5px;
    cursor: pointer;
    background-color: #ffffff
}

a {
    text-decoration: none;
    color: inherit
}

#recentlyViewedList {
  font-size: 0.8rem;
  color: rgba(0, 0, 0, 0.5);
}

.recentlyViewedItem {
  text-decoration: underline;
  cursor: pointer;
}

.recentlyViewedDivide {
  width: 20px;
  opacity: 0.2;
  position: relative;
  top: 5px;
}

h1 {
  margin-top: 0px;
  margin-bottom: 10px;
}

#nav {
  text-align: center;
  position: fixed;
  bottom: 0;
  width: 100%;
  overflow: hidden;
  background-color: #B9D9C3;
  z-index: 1;
  -webkit-box-shadow: 0px -5px 8px 0px rgba(0,0,0,0.1);
  -moz-box-shadow: 0px -5px 8px 0px rgba(0,0,0,0.1);
  box-shadow: 0px -5px 8px 0px rgba(0,0,0,0.1);
}

.nav-icon {
  width: 30px;
  margin: 10px 35px; 
  cursor: pointer;
  padding-top: 7px;
}

/* #nav::after {
    content: "";
    background: -webkit-linear-gradient(left, rgba(255, 255, 255, 0.5) 0%, rgba(228, 228, 228, 0.5) 15%, rgba(228, 228, 228, 0.5) 85%, rgba(255, 255, 255, 0.5) 100%);
    display: block;
    height:2px;
    width: 100%;
    position: absolute;
    bottom: 0;
} */

select {
    margin: 5px 15px 5px 0px;
    height: 2rem;
    font-size: 1rem;
    width: 100%;
    white-space: nowrap;
    overflow: hidden;
    text-overflow: ellipsis;
}

.icon {
    width: 10px;
    opacity: 0.4;
}

.icon:hover {
    opacity: 0.9;
}

.icon-big {
  width: 15px;
  opacity: 0.4;
}

.icon-big:hover {
    opacity: 0.9;
}

.customButton {
    font-size: 0.8rem;
    /* font-weight: 100; */
    position: relative;
    bottom: 3px;
    opacity: 0.4;
    border: solid 1px rgba(0, 0, 0,0.4);
    padding: 5px 5px;
    border-radius: 5px;
}

.customButton:hover {
    opacity: 0.9;
}

button {
  outline: none;
}

.divider {
    content: "";
    background: -webkit-linear-gradient(left, rgba(255, 255, 255, 0.7) 0%, rgba(228, 228, 228, 0.7) 15%, rgba(228, 228, 228, 0.7) 85%, rgba(255, 255, 255, 0.7) 100%);
    height: 1px;
    width: 100%;
/*     position: absolute;
    bottom: 0;
    display: block; */
}

.divider-light {
    content: "";
    background: -webkit-linear-gradient(left, rgba(255, 255, 255, 0.2) 0%, rgba(228, 228, 228, 0.2) 15%, rgba(228, 228, 228, 0.2) 85%, rgba(255, 255, 255, 0.2) 100%);
    height: 1px;
    width: 100%;
/*     position: absolute;
    bottom: 0;
    display: block; */
}

/* Padding styles */

.pl10 {
  padding-left: 10px
}

.pr10 {
  padding-right: 10px;
}

.pt10 {
  padding-top: 10px;
}

.pb10 {
  padding-bottom: 10px;
}

</style>
