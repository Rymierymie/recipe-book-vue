<template>
    <div>
        <div>
            <h1>Menu
                <button @click="clear_menu()">Clear menu</button>
            </h1>    
                <ul> 
                    <li v-for="(item, index) in menu" v-bind:key="index">
                        {{ item }}
                        <button @click="remove_from_menu(item)">Remove from menu</button>
                    </li>
                </ul>
        </div>

        <div id="recipe-card" class="display-toggle">
            <div class="divider">
            </div>
            <div v-bind:key="recipe_name"> 
                <h1 >{{ recipe_name }}
                <button @click="add_to_menu(recipe_name)">Add to menu</button>
                </h1>
            </div>
            <div v-bind:key="recipe_description" id="recipeDescription">
                <p><strong>Description:</strong> {{ recipe_description }}</p>
            </div>
            <div class="divider-light">
            </div>
            <div id="ingredientsContainer"> 
            <h2>Ingredients</h2>
                <div  v-for="(item, index) in recipe_ingredients" v-bind:key="index" :id="`${item.name}_menuList`" class="ingredientsListItem" @click="opacityToggle(item.name)"> 
                    <p class="ingredientItem">
                        <strong>{{ item.name }}</strong> {{ item.amount }}<span style="opacity:0;" v-if="item.type === 'whole' || item.type === 'cup' ">-</span>{{ item.type }}
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
                    <li v-for="(item, index) in recipe_method" v-bind:key="index" class="methodListItem">
                        {{ item.instruction }}
                    </li>
                </ol>
            </div> 
            <div class="divider">
            </div>
        </div> 

        <div>
<!--             <select id="recipe-select" v-model="recipe_name">
                <option selected></option>
                <option v-for="(item, index) in recipe_list" v-bind:key="index">
                    {{ item }}
                </option>
            </select> -->
            <button>View all recipes</button>
            <div v-for="(item, index) in recipesData" v-bind:key="index" class="recipeListItem">
                <h3>{{ item.recipe }}</h3>
                <p class="recipeDescription">{{ item.description }}</p>
                <button @click="add_to_menu(item.recipe)">Add to menu</button>
                <button @click="view_recipe(item.recipe)">View</button>
            </div>   
        </div>
        <div>
            <h3>Browse by ingredients:</h3>
            <select id="ingredients-list" v-model="ingredient_name">
                <option selected></option>
                <option v-for="(item, index) in ingredients_list" v-bind:key="index">
                    {{ item }}
                </option>
            </select>
        </div>
  
        <div id="ingredients-card" class="display-toggle">
            <h1>Ingredients with {{ ingredient_name }}</h1>
                <ul>
                    <li v-for="(item, index) in ingredient_recipes" v-bind:key="index">
                        {{ item }}
                        <button @click="view_recipe(item)">View</button>
                        <button @click="add_to_menu(item)">Add to menu</button>
                    </li>
                </ul>
        </div>
        <div v-if="related_recipe !== null" v-bind:key="related_recipe">
            <p><strong>Related Recipe:</strong> {{ related_recipe }} <button @click="view_recipe(related_recipe)">View</button></p>
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
          console.log(document.getElementById(item + "_menuList"));
          let elem = document.getElementById(item + "_menuList")
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
          
        },
      remove_from_menu(recipe){
          let index = this.menu.findIndex(x => x == recipe);
          this.menu.splice(index, 1); 
          eventBus.$emit('menu_edit', this.menu);         
          },
      clear_menu(){
          this.menu = []
          localStorage.removeItem('menu')
          }
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
}

.recipeListItem {
    margin: 20px 0px;
    padding: 10px 10px;
    border: solid 1px rgba(0, 0, 0,0.1);
    border-radius: 5px;
}

.methodListItem {
    font-size: 1.1rem;
    padding-bottom: 20px;
}

.divider {
    content: "";
    background: -webkit-linear-gradient(left, rgba(255, 255, 255, 0.7) 0%, rgba(228, 228, 228, 0.7) 15%, rgba(228, 228, 228, 0.7) 85%, rgba(255, 255, 255, 0.7) 100%);
    height: 2px;
    width: 100%;
/*     position: absolute;
    bottom: 0;
    display: block; */
}

.divider-light {
    content: "";
    background: -webkit-linear-gradient(left, rgba(255, 255, 255, 0.2) 0%, rgba(228, 228, 228, 0.2) 15%, rgba(228, 228, 228, 0.2) 85%, rgba(255, 255, 255, 0.2) 100%);
    height: 2px;
    width: 100%;
/*     position: absolute;
    bottom: 0;
    display: block; */
}

#recipeDescription {
    padding-bottom: 20px;
}

.ingredientItem {
    margin-bottom: 5px;
}

.ingredientsListItem {
    font-size: 1.2rem;
    margin: 20px 0px;
}

.ingredientNotes {
    font-size: 0.9rem;
    opacity: 0.6;
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

.opacity_toggle {
    opacity: 0.3;
}

</style>