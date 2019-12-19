<template>
    <div>
        <div>
            <h2>Menu</h2>
                <button @click="clear_menu()">Clear menu</button>
                <ul> 
                    <li v-for="(item, index) in menu" v-bind:key="index">
                        {{ item }}
                        <button @click="remove_from_menu(item)">Remove from menu</button>
                    </li>
                </ul>
        </div>
        <div>
            <select id="recipe-select" v-model="recipe_name">
                <option selected></option>
                <option v-for="(item, index) in recipe_list" v-bind:key="index">
                    {{ item }}
                </option>
            </select>
        </div>
        <div>
            <select id="ingredients-list" v-model="ingredient_name">
                <option selected></option>
                <option v-for="(item, index) in ingredients_list" v-bind:key="index">
                    {{ item }}
                </option>
            </select>
        </div>
        <div id="recipe-card" class="display-toggle">
            <div v-bind:key="recipe_name"> 
                <h1 >{{ recipe_name }}</h1>
                <button @click="add_to_menu(recipe_name)">Add to menu</button>
            </div>
            <div v-bind:key="recipe_description">
                <p><strong>Description:</strong> {{ recipe_description }}</p>
            </div>
            <div> 
            <h2>Ingredients</h2>
                <ul> 
                    <li v-for="(item, index) in recipe_ingredients" v-bind:key="index">
                        {{ item.name }} {{ item.amount }}{{ item.type }}
                    </li>
                </ul>
            </div> 
            <div> 
            <h2>Method</h2>
                <ol>
                    <li v-for="(item, index) in recipe_method" v-bind:key="index">
                        {{ item.instruction }}
                    </li>
                </ol>
            </div> 
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
        <div v-if="related_recipe != ''" v-bind:key="related_recipe">
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
              console.log("Already in the list!")
              }
      }
     
      }
   ingredients_list.sort()


   return {
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
          localStorage.removeItem('menu')
          }
    }, 
  mounted() {
      if (localStorage.menu) {
      this.menu = JSON.parse(localStorage.getItem('menu'))
    }
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

</style>