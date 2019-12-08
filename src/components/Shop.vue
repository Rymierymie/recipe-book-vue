<template>
    <div>
        <h1>Shop</h1>
        <p>{{ meal_plan }}</p>
        <p>{{ shopping_list }}</p>
    </div>
    
</template>

<script>
/* import Recipes from '../assets/Recipes.json'; */

import { eventBus } from '../event-bus';

export default {
    data ()  {
        return {
            meal_plan: {},
            shopping_list: []
        }
    },
    methods: {
       shopping_list_builder: function(meal_object){
    /* Code from V2  */   
        let days = ['monday','tuesday','wednesday','thursday','friday','saturday','sunday'];
        let times = ['lunch','dinner'];
        let meals = []
        /* let list = [] */
        let meal_plan = meal_object

    // Going through every planned meal
        days.forEach(day => {
            times.forEach(time => {
                let r = meal_plan[day][time].recipe;
                let s = meal_plan[day][time].serves;

                //using this method for finding the index of an element below: https://www.geeksforgeeks.org/javascript-array-findindex-method/
                function indexOf(element) {
                    return (Object.keys(element) == r);
                  }

                if (r != ""){
                    let meal = {
                        [r] : s
                    }
                    if (meals.findIndex(indexOf) == -1){
                        meals.push(meal)
                    } else {
                        //method for accessing the keys/items of an object: https://stackoverflow.com/questions/983267/how-to-access-the-first-property-of-an-object-in-javascript
                        let i = meals.findIndex(indexOf)
                        let key = Object.keys(meals[i])
                        meals[i][key] += Number(s)
                    }
                }
            })
        })
        console.log(meals)

        /* Move the rest of V2 list builder logic in here */

        /* End code from V2 */

       }
    },
    mounted () {
        if (localStorage.meal_plan) {
            this.meal_plan = JSON.parse(localStorage.getItem('meal_plan'));
            this.shopping_list_builder(this.meal_plan);
            }
        eventBus.$on('meal_plan_edit', new_meal_plan => {
                    this.meal_plan = new_meal_plan;
                    this.shopping_list_builder(this.meal_plan);
                    });
        
        
    }
}
</script>


<style scoped>

</style>