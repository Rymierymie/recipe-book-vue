<template>
    <div class="componentDiv">
        <div id="recipe-card" class="display-toggle">
            <div class="ml10" v-bind:key="recipe_name"> 
                <h1 class="mt10">{{ recipe_name }}</h1>
            </div>
            <div class="ml10" v-bind:key="recipe_description">
                <p>{{ recipe_description }}</p>
            </div>
            <div class="ml10">
            <button v-if="!menu.includes(recipe_name)" @click="add_to_menu(recipe_name)" class="c-button c-button-fill headlineButton">Add to menu</button>
            <button v-if="menu.includes(recipe_name)" class="c-button c-button-fill headlineButton" @click="remove_from_menu(recipe_name)">Remove from menu</button>
            <img v-if="menu.includes(recipe_name)" src="../assets/icons/calendar-check.png" class="icon-bigger in-menu-icon" />
            </div>
            <div class="ml10" id="relatedRecipeDiv" v-if="related_recipe !== null" v-bind:key="related_recipe">
            <p><strong>Related Recipe:</strong> {{ related_recipe }}</p>
            <p><a href="#recipe-card"><button class="c-button" @click="view_recipe(related_recipe)">View</button></a></p>
            </div>
            <div id="ingredientsContainer"> 
            <h3 class="ml10 mb10">Ingredients</h3>
                <div  v-for="(item, index) in recipe_ingredients" v-bind:key="index" :id="`${item.name}`" class="card list-card" @click="opacityToggle(item.name)"> 
                    <h2 class="mb0">{{ item.name.charAt(0).toUpperCase() + item.name.slice(1) }}</h2>
                    <h3 class="mb0 mt0">{{ item.amount }}<span style="opacity:0;" v-if="item.type === 'whole' || item.type === 'cup' || item.type === 'clove' ">-</span>{{ item.type }}</h3>
                    <p class="mb0 mt0">{{ item.notes }}</p>
                </div>
            </div> 
            <div> 
            <h3 class="ml10">Method</h3>
                <ul>
                    <li v-for="(item, index) in recipe_method" v-bind:key="index" class="methodListItem card" :id="`${item.step_number}`" @click="opacityToggle(item.step_number)">
                        <div class="counter-round mr5">{{ item.step_number }}</div>
                        {{ item.instruction }}
                    </li>
                </ul>
            </div> 
        </div> 

        <div>
<!--             <select id="recipe-select" v-model="recipe_name">
                <option selected></option>
                <option v-for="(item, index) in recipe_list" v-bind:key="index">
                    {{ item }}
                </option>
            </select> -->
           <!--  <button>View all recipes</button> -->
           <div class="ml10 mr10 mb10">
               <span id="allRecipesHeadline">Filter Recipes
                   <span id="vegoFilterCheckbox"><span style="padding-right:5px">Vegetarian</span>
                        <label class="switch">
                            <input type="checkbox" v-model="vegoFilter">
                            <span class="slider round"></span>
                        </label>
                   </span>
                </span> 
           </div>
           <div id="filterButtons" class="ml10">
               <button class="c-button btn active mb10" @click="filterSelection('all')">Show all</button>
               <template v-for="(item,index) in recipeCategories">
                   <button class="c-button btn mb10" v-bind:key="index" @click="filterSelection(item)">{{ item }}</button>
               </template>
           </div>
           <template v-for="(item, index) in recipesData">
            <div v-bind:key="index" :class="`${item.vegetarian}`" class="card" >
                <div v-bind:class="[item.category]" class="filterDiv show">
                    <a href="#recipe-card" @click="view_recipe(item.recipe)">
                    <h2 class="mb0">{{ item.recipe }}</h2>
                    <p class="mb20 mt5">{{ item.description }}</p></a>
                    <button v-if="!menu.includes(item.recipe)" class="c-button c-button-fill" @click="add_to_menu(item.recipe)">Add to menu</button>
                    <button v-if="menu.includes(item.recipe)" class="c-button c-button-fill" @click="remove_from_menu(item.recipe)">Remove from menu</button>  
                    <img v-if="menu.includes(item.recipe)" src="../assets/icons/calendar-check.png" class="icon-bigger in-menu-icon" />
                    <!-- <button class="c-button c-button-fill" >View</button> -->
                </div>   

<!--            Attempting here to put the ingredients and steps with in the cards, rather than at the top of the component. 
                Needs to be that that the section only renders if the name of the recipe matches the selected one. 
                So needs to sit within the v-for of the main recipe card  -->  
                <div v-if="recipe_name === item.recipe" id="ingredientsContainer"> 
                    <h3 class="ml10 mb10">Ingredients</h3>
                    <div  v-for="(item, index) in recipe_ingredients" v-bind:key="index" :id="`${item.name}`" @click="opacityToggle(item.name)"> 
                        <h2 class="mb0">{{ item.name.charAt(0).toUpperCase() + item.name.slice(1) }}</h2>
                        <h3 class="mb0 mt0">{{ item.amount }}<span style="opacity:0;" v-if="item.type === 'whole' || item.type === 'cup' || item.type === 'clove' ">-</span>{{ item.type }}</h3>
                        <p class="mb0 mt0">{{ item.notes }}</p>
                    </div>
                </div> 
                <div v-if="recipe_name === item.recipe"> 
                    <h3 class="ml10">Method</h3>
                        <ul>
                            <li v-for="(item, index) in recipe_method" v-bind:key="index" class="methodListItem" :id="`${item.step_number}`" @click="opacityToggle(item.step_number)">
                                <div class="counter-round mr5">{{ item.step_number }}</div>
                                {{ item.instruction }}
                            </li>
                        </ul>
                 </div> 
            </div>
            </template>
        </div>
        <div id="ingredientSelectDiv">
            <h3 class="ml10 mb5">Browse by ingredients</h3>
            <select class="custom-select ml10 mr10 pl10" v-model="ingredient_name">
                <option selected></option>
                <option v-for="(item, index) in ingredients_list" v-bind:key="index">
                    {{ item }}
                </option>
            </select>
        </div>
  
        <div id="ingredients-card" class="display-toggle">
            <h3 class="ml10 mb0 pb0">Recipes with {{ ingredient_name }}</h3>
                    <div v-for="(item, index) in ingredient_recipes" v-bind:key="index" class="card list-card">
                        <h2 class="mb15" @click="view_recipe(item)">{{ item }}</h2>
                        <button v-if="!menu.includes(item)" class="c-button c-button-fill" @click="add_to_menu(item)">Add to menu</button>
                        <button v-if="menu.includes(item)" class="c-button c-button-fill" @click="remove_from_menu(item)">Remove from menu</button>
                        <img v-if="menu.includes(item)" src="../assets/icons/calendar-check.png" class="icon-bigger in-menu-icon" />
                    </div>
        </div>
        <div style="padding-bottom: 100px;"></div>
    </div>
</template>

<script>
import Recipes from '../assets/Recipes.json';

import { eventBus } from '../event-bus';


export default {
  data () {
  let recipe_list = [];
  let ingredients_list = [];
  for (var recipe in Recipes){
      recipe_list.push(Recipes[recipe].recipe)
      for (var ingredient in Recipes[recipe].ingredients){
          let name = Recipes[recipe].ingredients[ingredient].name
          if (ingredients_list.findIndex(x => x == name) == -1){
              ingredients_list.push(name);
          } else {
              //console.log("Already in the list!")
              }
      }
     
      }
   ingredients_list.sort()
   let recipesData = Recipes;

   return {
      recipesData: recipesData,
      recipe_list: recipe_list,
      ingredients_list: ingredients_list,
      ingredient_name: null,
      ingredient_recipes: null,
      recipe_name: null,
      recipe_ingredients: null,
      recipe_method: null,
      recipe_description: null,
      related_recipe: null,
      menu: [],
      vegoFilter: false,
      recipeCategories: []
        }   
  },
  methods: {
      filterSelection(c){
        var x, i;
        x = document.getElementsByClassName("filterDiv");
        if (c == "all") c = "";
        // Add the "show" class (display:block) to the filtered elements, and remove the "show" class from the elements that are not selected
        for (i = 0; i < x.length; i++) {
            this.removeClass(x[i], "show");
            if (x[i].className.indexOf(c) > -1) this.addClass(x[i], "show");
        }
      },
      addClass(element, name) {
        var i, arr1, arr2;
        arr1 = element.className.split(" ");
        arr2 = name.split(" ");
        for (i = 0; i < arr2.length; i++) {
            if (arr1.indexOf(arr2[i]) == -1) {
            element.className += " " + arr2[i];
            }
        }
        },
      removeClass(element, name) {
        var i, arr1, arr2;
        arr1 = element.className.split(" ");
        arr2 = name.split(" ");
        for (i = 0; i < arr2.length; i++) {
            while (arr1.indexOf(arr2[i]) > -1) {
            arr1.splice(arr1.indexOf(arr2[i]), 1);
            }
        }
        element.className = arr1.join(" ");
        },  
      opacityToggle(item){
          console.log(document.getElementById(item));
          let elem = document.getElementById(item)
          elem.classList.toggle("opacity_toggle")
      },
      view_recipe(recipe){
          this.recipe_name = recipe;
          let opacityElems = document.getElementsByClassName("opacity_toggle");
          console.log(opacityElems);
          Array.from(opacityElems).forEach(function (element) { 
                    console.log(element) 
                    element.classList.toggle("display-toggle");
                });
          },
      add_to_menu(recipe){
          if (this.menu.findIndex(x => x == recipe) == -1){
            console.log("adding to menu");
            this.menu.push(recipe);
            console.log(this.menu);
            eventBus.$emit('menu_edit', this.menu);
          } else {
              alert("That's already on your menu!");
              }  
          
        },
       remove_from_menu(recipe){
          let index = this.menu.findIndex(x => x == recipe);
          this.menu.splice(index, 1); 
          eventBus.$emit('menu_edit', this.menu);         
          },
    /*  clear_menu(){
          this.menu = []
          localStorage.removeItem('menu')
          } */
    }, 
  created() {

      Recipes.forEach(item => {
          let cats = item.category;
          //console.log(cats)
          cats.forEach(cat => {
             let index = this.recipeCategories.findIndex(x => x === cat);
             //console.log(index);
             if (index === -1){
                 this.recipeCategories.push(cat)
             }
          })
      });
      console.log(this.recipeCategories);
      this.filterSelection("all");
  },  
  mounted() {
      if (localStorage.menu) {
      this.menu = JSON.parse(localStorage.getItem('menu'))
    }
    eventBus.$on('viewRecentClick', item => {
                this.view_recipe(item);
                //this.recipe_name = item;
                });

// Stolen code for the filter buttons
    var btnContainer = document.getElementById("filterButtons");
        var btns = btnContainer.getElementsByClassName("btn");
        //console.log(btns);
        for (var i = 0; i < btns.length; i++) {
        btns[i].addEventListener("click", function() {
            var current = document.getElementsByClassName("active");
            current[0].className = current[0].className.replace(" active", "");
            this.className += " active";
        });
        }
  },
  watch: {
      vegoFilter: function(){
          // If the vego filter is true, need to hide all the recipes with a class of false
          if (this.vegoFilter === true){
              console.log("Vego filter set to true");
              let elems = document.getElementsByClassName('false');
              console.log(elems);
              Array.from(elems).forEach(function (element) { 
                    console.log(element) 
                    element.classList.add("non-vego");
                });
          }
          // If the vego filter is false, need to show all. Remove any classes that are hiding the non-vego meals
          if (this.vegoFilter === false){
              console.log("Vego filter set to false");
              let elems = document.getElementsByClassName('non-vego');
              console.log(elems);
              Array.from(elems).forEach(function (element) { 
                    console.log(element) 
                    element.classList.remove("non-vego");
                });
          }
      },
      recipe_name: function() {
                if (document.getElementById("recipe-card").classList.contains("display-toggle")){
                    let element = document.getElementById("recipe-card");
                    element.classList.toggle("display-toggle");
                }
                let index = Recipes.findIndex(x => x.recipe == this.recipe_name);
                this.recipe_name = Recipes[index].recipe;
                this.recipe_ingredients = Recipes[index].ingredients;
                this.recipe_method = Recipes[index].method; 
                this.recipe_description = Recipes[index].description; 
                this.related_recipe = Recipes[index].related_recipe;
                /* let recentlyViewed = JSON.parse(localStorage.getItem('recentlyViewed'));
                recentlyViewed.push(this.recipe_name);
                localStorage.setItem('recentlyViewed', JSON.stringify(recentlyViewed)) */
                eventBus.$emit('recentView', this.recipe_name);

          },
      ingredient_name: function() {
                let with_ingredient = []
                if (document.getElementById("ingredients-card").classList.contains("display-toggle")){
                    let element = document.getElementById("ingredients-card");
                    element.classList.toggle("display-toggle");
                }
                let ingredient = this.ingredient_name;
                for (var recipe in Recipes){
                    if (Recipes[recipe].ingredients.findIndex(x => x.name == ingredient) != -1){
                    with_ingredient.push(Recipes[recipe].recipe)
                    }
                }
                this.ingredient_recipes = with_ingredient             
          },
      menu: function() {
                localStorage.setItem('menu', JSON.stringify(this.menu)) 
          }
      

    }
}      


</script>


<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

.display-toggle {
  display: none;
}

ul {
    list-style-type:none;
    padding: 0px 0px;
    margin: 0px 0px;
}

ol {
    padding-left: 20px;
    margin: 0px 0px;
}

h2 {
      color: #7DC092;
}


.methodListItem {
    cursor: pointer;
}


#filterButtons {
    margin-bottom: -15px;
}

#filterButtons .c-button {
    margin-right: 10px;
}


.opacity_toggle {
    opacity: 0.3;
}


#ingredients-card h4 {
    margin: 0px 0px -15px 0px;
}

#ingredientSelectDiv h4 {
    margin: 15px 0px 0px 0px;
}

.headlineButton {
    /* float: right; */
    margin: 10px 10px 10px 0px;
}

#allRecipesHeadline {
    margin-bottom: -10px;
    font-weight: 200;
    margin-top: 20px;
}


#relatedRecipeDiv {
    padding: 20px 0px;
}

#vegoFilterCheckbox {
    position: relative;
    float: right;
    top: 5px;
    font-size:0.8rem;
}

/* Stolen styles for the filtering */

.filterDiv {
    display: none;
}

.show {
  display: block;
}

/* button.active {
  background-color: rgba(125, 192, 146, 1);
  color: #ffffff;
}

button.active:hover {
  background-color: #649A75;
  border: solid 0.5px #649A75;
  -webkit-box-shadow: 0px 5px 20px 0px rgba(0,0,0,0.1);
  -moz-box-shadow: 0px 5px 20px 0px rgba(0,0,0,0.1);
  box-shadow: 0px 5px 20px 0px rgba(0,0,0,0.1);
}
 */
/* End of styles for filtering */

/* Start of VERY stolen styles for rounded switch */

/* The switch - the box around the slider */
.switch {
  position: relative;
  display: inline-block;
  width: 30px;
  height: 17px;
}

/* Hide default HTML checkbox */
.switch input {
  opacity: 0;
  width: 0;
  height: 0;
}

/* The slider */
.slider {
  position: absolute;
  cursor: pointer;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: #ccc;
  -webkit-transition: .4s;
  transition: .4s;
}

.slider:before {
  position: absolute;
  content: "";
  height: 17px;
  width: 17px;
  left: -1px;
  bottom: 0px;
  background-color: white;
  -webkit-transition: .4s;
  transition: .4s;
  box-shadow: 0 0 2px #6D6D6D;
}

input:checked + .slider {
  background-color: rgba(185, 217, 195, 1);
}

input:focus + .slider {
  box-shadow: 0 0 2px #6D6D6D;
}

input:checked + .slider:before {
  -webkit-transform: translateX(15px);
  -ms-transform: translateX(15px);
  transform: translateX(15px);
}

/* Rounded sliders */
.slider.round {
  border-radius: 34px;
}

.slider.round:before {
  border-radius: 50%;
}

/* End of styles for rounded switch */

.non-vego {
    display: none;
}

.counter-round {
  width: 26px;
  line-height:26px;
  text-align: center;
  border: solid 0.5px rgba(125, 192, 146, 1);
  background-color: rgba(125, 192, 146, 1);
  color: #ffffff;
  border-radius: 50px;
  display: inline-block;
}

.in-menu-icon {
    position: relative;
    float: right;
    right: 10px;
}

</style>