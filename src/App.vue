<template>
  <div id="app">
    <link href="https://fonts.googleapis.com/css?family=Poppins:200,400,600&display=swap" rel="stylesheet">
    <link href="https://fonts.googleapis.com/css?family=Roboto:300&display=swap" rel="stylesheet">
  <div id="nav">  
    <img src="./assets/icons/utensils.png" class="nav-icon" @click="display('search')" />
    <img src="./assets/icons/calendar.png" class="nav-icon" @click="display('plan')" />
    <img src="./assets/icons/tasks.png" class="nav-icon" @click="display('shop')" />
  </div>  
  <div style="padding-left:15px;" v-if="selected === 'search'">
    <p v-if="this.recentlyViewed.length !== 0" id="recentlyViewedList"><span class="mr10">Recently Viewed</span>
      <a href="#recipe-card"><button class="c-button c-button-recent mr10" v-for="(item, index) in recentlyViewed" v-bind:key="index" @click="viewRecentView(item)">{{ item.substring(0, 20) + '...' }}</button></a>
    </p>
  </div>
    <div v-if="this.selected=='search'" id="search-component">
      <search />
    </div>  
    <div v-if="this.selected=='plan'" id="plan-component">  
      <mealplanner />
    </div>
    <div v-show="this.selected=='shop'" id="shop-component">
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
/*     recentlyViewed: function(){
      console.log("this changed");
      if (this.recentlyViewed.length > 1){
        let items = document.getElementsByClassName("recentlyViewedDivide");
        let index = items.length - 1;
        console.log(items);
        console.log(index);
        items[index].style.display = 'none'
        
      }
    } */
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
  color: #6D6D6D;
  background-color: #fff;
  /* background: url("./assets/woodbackground.jpg") no-repeat fixed center;  */
}

.componentDiv {
  margin: 5px 0px;
}


.card {
    margin: 20px 10px;
    padding: 10px 10px;
    border: solid 0.5px rgba(180, 194, 211, 0.2);
    border-radius: 10px;
    background-color: #ffffff;
    -webkit-box-shadow: 0px 5px 20px 0px rgba(0,0,0,0.1);
    -moz-box-shadow: 0px 5px 20px 0px rgba(0,0,0,0.1);
    box-shadow: 0px 5px 20px 0px rgba(0,0,0,0.1);
}

a {
    text-decoration: none;
    color: inherit;
    cursor: pointer;
}

p {
  /* font-family: 'Roboto', sans-serif; */
  font-weight: 200;
  font-size: 13px;
  line-height: 22px;
}

h1 {
  font-weight: 200;
  font-size: 26px;
  line-height: 41px;
  margin-top: 0px;
  margin-bottom: 10px;
}

h2 {
  font-weight: 200;
  font-size: 20px;
  line-height: 32px;
  margin-top: 0px;
}

h3 {
  font-weight: 200;
  font-size: 16px;
  line-height: 26px;
}

h4 {
  font-weight: 200;
  font-size: 13px;
  line-height: 22px;
}

li {
  font-weight: 200;
  font-size: 16px;
  line-height: 26px;
}

#nav {
  text-align: center;
  position: fixed;
  bottom: 0;
  width: 100%;
  overflow: hidden;
  background-color: rgba(125, 192, 146, 1);
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

.c-button {
    font-size: 13px;
    font-weight: 100;
    color: rgba(125, 192, 146, 1);
    line-height: 22px;
    position: relative;
    bottom: 3px;
    opacity: 1;
    border: solid 0.5px rgba(125, 192, 146, 1);
    padding: 3px 15px;
    border-radius: 12px;
}

.c-button-fill {
  background-color: rgba(125, 192, 146, 1);
  color: #ffffff;
}

.c-button-recent {
  border: solid 0.5px rgba(229, 229, 229, 1);
  color: #6d6d6d;
}

.c-button:hover {
  background-color: rgba(125, 192, 146, 1);
  color: #ffffff;
}

.c-button-recent:hover {
  border: solid 0.5px rgba(125, 192, 146, 1);
  color: rgba(125, 192, 146, 1);
  background-color: #ffffff
}

.c-button-fill:hover {

}

.c-button-round: {
/*   width: 42px;
  border: solid 0.5px rgba(125, 192, 146, 1);
  background-color: rgba(125, 192, 146, 1);
  color: #ffffff;
  border-radius: 50px; */
}

button {
  outline: none;
  cursor: pointer;
}

hr {

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

/* Margin styles */
.ml10 {
  margin-left: 10px
}

.mr10 {
  margin-right: 10px;
}

.mt10 {
  margin-top: 10px;
}

.mb10 {
  margin-bottom: 10px;
}

.ml5 {
  margin-left: 5px
}

.mr5 {
  margin-right: 5px;
}

.mt5 {
  margin-top: 5px;
}

.mb5 {
  margin-bottom: 5px;
}

/* Start of stolen custom dropdown styles */



/* End of stolen custom dropdown styles */

</style>
