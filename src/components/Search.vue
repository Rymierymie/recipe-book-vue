<template>
    <div>
        <div>
            <select id="recipe-select" v-model="recipe_name">
            <option v-for="recipe in recipe_list" v-bind:key="recipe">
                {{ recipe }}
            </option>
            </select>
        </div>
        <div>
            <input type="text" list="ingredients-list">
            <datalist id="ingredients-list">
            <option v-for="ingredient in ingredients_list" v-bind:key="ingredient">
                {{ ingredient }}
            </option>
            </datalist>
        </div>
<!--         <div>
            <button v-on:click="recipe_builder()">Show Recipe</button>
        </div> -->
        <div>
            <h1 v-bind:key="recipe_name">{{ recipe_name }}</h1>
            <h2>Ingredients</h2>
            <ul> 
                <li v-for="item in recipe_ingredients" v-bind:key="item">
                    {{ item.name }} {{ item.amount }}{{ item.type }}
                </li>
            </ul>
            <h2>Method</h2>
            <ol>
                <li v-for="step in recipe_method" v-bind:key="step">
                    {{ step.instruction }}
                </li>
            </ol>
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
                console.log("recipe name changed");
                let index = Recipes.findIndex(x => x.recipe == this.recipe_name);
                this.recipe_name = Recipes[index].recipe;
                this.recipe_ingredients = Recipes[index].ingredients;
                this.recipe_method = Recipes[index].method; 

          }
      

    }
}      


</script>


<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>