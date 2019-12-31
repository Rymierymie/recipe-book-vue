<template>
    <div class="componentDiv">
        <div id="recipe-card" class="display-toggle">
            <div class="divider">
            </div>
            <div v-bind:key="recipe_name"> 
                <h1 id="recipeHeadline">{{ recipe_name }}
                </h1>
            </div>
            <div v-bind:key="recipe_description" id="recipeDescription">
                <p class="recipeDescriptionPara">{{ recipe_description }}</p>
            </div>
            <span @click="add_to_menu(recipe_name)" class="customButton" id="headlineButton">Add to menu</span>
            <div style="margin-top:20px;" class="divider-light">
            </div>
            <div id="ingredientsContainer"> 
            <h2>Ingredients</h2>
                <div  v-for="(item, index) in recipe_ingredients" v-bind:key="index" :id="`${item.name}`" class="ingredientsListItem" @click="opacityToggle(item.name)"> 
                    <p class="ingredientItem">
                        <strong>{{ item.name }}</strong> {{ item.amount }}<span style="opacity:0;" v-if="item.type === 'whole' || item.type === 'cup' || item.type === 'clove' ">-</span>{{ item.type }}
                    </p>
                    <p class="ingredientNotes">
                        {{ item.notes }}
                    </p>
                </div>
            </div> 
            <div class="divider-light">
            </div>
            <div> 
            <h2>Method</h2>
                <ol>
                    <li v-for="(item, index) in recipe_method" v-bind:key="index" class="methodListItem" :id="`${item.step_number}`" @click="opacityToggle(item.step_number)">
                        {{ item.instruction }}
                    </li>
                </ol>
            </div> 
            <div class="divider">
            </div>
            <div v-if="related_recipe !== null" v-bind:key="related_recipe">
            <p><strong>Related Recipe:</strong> {{ related_recipe }} <button @click="view_recipe(related_recipe)">View</button></p>
            <div class="divider">
            </div>
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
           <h4 id="allRecipesHeadline">All Recipes</h4>
            <div v-for="(item, index) in recipesData" v-bind:key="index" class="recipeListItem">
                <h3>{{ item.recipe }}</h3>
                <p class="recipeDescription">{{ item.description }}</p>
                <span class="customButton" @click="add_to_menu(item.recipe)">Add to menu</span>
                <a href="#recipe-card"><span class="customButton" @click="view_recipe(item.recipe)">View</span></a>
            </div>   
        </div>
        <div class="divider">
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
                    <div v-for="(item, index) in ingredient_recipes" v-bind:key="index" class="recipeListItem">
                        <h3>{{ item }}</h3>
                        <span class="customButton" @click="add_to_menu(item)">Add to menu</span>
                        <span class="customButton" @click="view_recipe(item)">View</span>
                    </div>
        </div>
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
      menu: []
        }   
  },
  methods: {
      opacityToggle(item){
          console.log(document.getElementById(item));
          let elem = document.getElementById(item)
          elem.classList.toggle("opacity_toggle")
      },
      view_recipe(recipe){
          this.recipe_name = recipe;
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
  mounted() {
      if (localStorage.menu) {
      this.menu = JSON.parse(localStorage.getItem('menu'))
    }
    eventBus.$on('viewRecentClick', item => {
                this.recipe_name = item;
                });
  },
  watch: {
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

.recipeDescription {
    font-size: 0.9rem;
    font-weight: 200;
}

.recipeListItem {
    margin: 20px 0px;
    padding: 10px 10px;
    border: solid 1px rgba(0, 0, 0,0.1);
    border-radius: 5px;
    cursor: pointer;
    background-color: #ffffff
}

#recipeDescription {
    padding-bottom: 20px;
}

.methodListItem {
    font-size: 1.1rem;
    padding-bottom: 20px;
    line-height: 1.7rem;
    color: #333333;
    cursor: pointer;
}

#ingredients-list {
    margin-bottom: 20px;
}



.ingredientItem {
   /*  margin-bottom: 5px; */
}

.ingredientsListItem {
    font-size: 1.2rem;
    margin: 20px 0px;
    cursor: pointer;
}

.ingredientNotes {
    font-size: 0.9rem;
    opacity: 0.6;
    font-weight: 200;
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

button {
    margin-top: 10px;
    margin-right: 20px;
}


.recipeListItem .customButton {
    margin: 10px 10px 10px 0px;
}

.recipeDescription {
    padding-bottom: 20px;
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

#recipeHeadline {
    margin-top: 30px;
    /* margin-bottom: 20px; */
    /* padding-bottom: 20px; */
    font-size: 26px;
    font-weight: 400;
}

#headlineButton {
    /* float: right; */
    margin: 10px 10px 10px 0px;
    font-weight: 200;
}

#allRecipesHeadline {
    margin-bottom: -10px;
    font-weight: 200;
}

.browseByHeadline {
    font-weight: 200;
}

.recipeDescriptionPara {
    font-weight: 200;
}

h2 {
    font-weight: 400;
}

</style>