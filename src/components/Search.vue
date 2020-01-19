<template>
    <div class="componentDiv">
        <div id="recipe-card" class="display-toggle">
            <div class="ml10" v-bind:key="recipe_name"> 
                <h1 class="recipe-headline">{{ recipe_name }}</h1>
            </div>
            <div class="ml10" v-bind:key="recipe_description" id="recipeDescription">
                <p>{{ recipe_description }}</p>
            </div>
            <div class="ml10">
            <button @click="add_to_menu(recipe_name)" class="c-button c-button-fill" id="headlineButton">Add to menu</button>
            </div>
            <div class="ml10" id="relatedRecipeDiv" v-if="related_recipe !== null" v-bind:key="related_recipe">
            <p><strong>Related Recipe:</strong> {{ related_recipe }}</p>
            <p><a href="#recipe-card"><button class="c-button" @click="view_recipe(related_recipe)">View</button></a></p>
            </div>
            <div id="ingredientsContainer"> 
            <h3 class="ml10">Ingredients</h3>
                <div  v-for="(item, index) in recipe_ingredients" v-bind:key="index" :id="`${item.name}`" class="card" @click="opacityToggle(item.name)"> 
                    <h2>
                        {{ item.name }} {{ item.amount }}<span style="opacity:0;" v-if="item.type === 'whole' || item.type === 'cup' || item.type === 'clove' ">-</span>{{ item.type }}
                    </h2>
                    <p>
                        {{ item.notes }}
                    </p>
                </div>
            </div> 
            <div> 
            <h3>Method</h3>
                <ul>
                    <li v-for="(item, index) in recipe_method" v-bind:key="index" class="methodListItem card" :id="`${item.step_number}`" @click="opacityToggle(item.step_number)">
                        <button class="c-button-round">{{ item.step_number }}</button>
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
           <div id="headlineDiv" class="ml10">
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
               <button class="c-button btn active" @click="filterSelection('all')">Show all</button>
               <template v-for="(item,index) in recipeCategories">
                   <button class="c-button btn" v-bind:key="index" @click="filterSelection(item)">{{ item }}</button>
               </template>
           </div>
           <template v-for="(item, index) in recipesData">
            <div v-bind:key="index" :class="`${item.vegetarian}`">
                <div v-bind:class="[item.category]" class="recipeListItem card filterDiv show">
                    <a href="#recipe-card" @click="view_recipe(item.recipe)"><h2>{{ item.recipe }}</h2>
                    <p>{{ item.description }}</p></a>
                    <button class="c-button c-button-fill" @click="add_to_menu(item.recipe)">Add to menu</button>
                    <!-- <button class="c-button c-button-fill" >View</button> -->
                </div>   
            </div>
            </template>
        </div>
        <div id="ingredientSelectDiv">
            <h4 class="browseByHeadline">Browse by ingredients</h4>
            <select id="ingredients-list" v-model="ingredient_name">
                <option selected></option>
                <option v-for="(item, index) in ingredients_list" v-bind:key="index">
                    {{ item }}
                </option>
            </select>
        </div>
  
        <div id="ingredients-card" class="display-toggle">
            <h4 class="browseByHeadline">Recipes with {{ ingredient_name }}</h4>
                    <div v-for="(item, index) in ingredient_recipes" v-bind:key="index" class="recipeListItem card">
                        <h3>{{ item }}</h3>
                        <button class="c-button" @click="add_to_menu(item)">Add to menu</button>
                        <button class="c-button" @click="view_recipe(item)">View</button>
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
          
        }
/*       remove_from_menu(recipe){
          let index = this.menu.findIndex(x => x == recipe);
          this.menu.splice(index, 1); 
          eventBus.$emit('menu_edit', this.menu);         
          },
      clear_menu(){
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

p {
    margin: 0px 0px;
}

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

.recipeListItem {
    padding: 10px 10px;
}

#recipeDescription {
    padding-bottom: 20px;
}

.methodListItem {
    padding-bottom: 20px;
    cursor: pointer;
}

#ingredients-list {
    margin-bottom: 20px;
}


h2 {
    margin-bottom: 10px;
}

h3 {
    margin-bottom: 5px;
    margin-top: 0px;
}

h1 {
    margin-top: 10px;
}


.recipeListItem .c-button {
    margin: 10px 10px 10px 0px;
}

#filterButtons .c-button {
    margin-right: 10px;
}


.opacity_toggle {
    opacity: 0.3;
}

#ingredients-card h3 {
    padding-bottom: 10px;
}

#ingredients-card h4 {
    margin: 0px 0px -15px 0px;
}

#ingredientSelectDiv h4 {
    margin: 15px 0px 0px 0px;
}

.recipe-headline {
    margin-top: 30px;
}

#headlineButton {
    /* float: right; */
    margin: 10px 10px 10px 0px;
    font-weight: 200;
}

#allRecipesHeadline {
    margin-bottom: -10px;
    font-weight: 200;
    margin-top: 20px;
}

.browseByHeadline {
    font-weight: 200;
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

button.active {
  background-color: rgba(125, 192, 146, 1);
  color: #ffffff;
}

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
  box-shadow: 0 0 2px rgba(185, 217, 195, 1);
}

input:checked + .slider {
  background-color: rgba(185, 217, 195, 1);
}

input:focus + .slider {
  box-shadow: 0 0 2px rgba(185, 217, 195, 1);
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

#headlineDiv {
    padding-top: 15px;
    padding-bottom: 10px;
}

</style>