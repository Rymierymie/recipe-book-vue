<template>
    <div>
        <div>
            <select id="recipe-select" v-model="recipe_name">
            <option v-for="(item, index) in recipe_list" v-bind:key="index">
                {{ item }}
            </option>
            </select>
        </div>
        <div>
            <select id="ingredients-list" v-model="ingredient_name">
            <option v-for="(item, index) in ingredients_list" v-bind:key="index">
                {{ item }}
            </option>
            </select>
        </div>
        <div id="recipe-card" class="display-toggle">
            <div> 
                <h1 v-bind:key="recipe_name">{{ recipe_name }}</h1>
            </div>
            <div> 
            <h2>Ingredients</h2>
                <ul> 
                    <li v-for="(item, index) in recipe_ingredients" v-bind:key="index">
                        {{ item.name }} {{ index.amount }}{{ index.type }}
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
                    </li>
                </ul>

        </div>
    </div>
</template>

<script>
import Recipes from '../assets/Recipes.json';

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
        }   
  },
  methods: {
      /* recipe_builder(){
          let index = Recipes.findIndex(x => x.recipe == this.recipe_name);
          this.recipe_name = Recipes[index].recipe;
          this.recipe_ingredients = Recipes[index].ingredients;
          this.recipe_method = Recipes[index].method; 
        } */
    }, 
  watch: {
      recipe_name: function() {
                if (document.getElementById("recipe-card").classList.contains("display-toggle")){
                    let element = document.getElementById("recipe-card");
                    element.classList.toggle("display-toggle");
                }
                console.log("recipe name changed");
                let index = Recipes.findIndex(x => x.recipe == this.recipe_name);
                this.recipe_name = Recipes[index].recipe;
                this.recipe_ingredients = Recipes[index].ingredients;
                this.recipe_method = Recipes[index].method; 

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
                console.log(with_ingredient)
                
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