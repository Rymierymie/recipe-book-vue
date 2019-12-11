<template>
    <div>
        <h1>Shop</h1>
        <button @click="meal_plan_summary_builder()">Summary Builder</button>
        <h2>Summary</h2>
        {{ meal_plan_summary }}
        <ul> 
            <li v-for="(item, index) in shopping_list" v-bind:key="index" >
                <span v-if="item.amount != 0">
                    <input type="checkbox" class="checkbox" @click="check_click(item.item)">
                    {{ item.item }} {{ item.amount }} {{ item.type }}
                </span>
            </li>
        </ul>
        <h2>Shopping List</h2>
        {{ shopping_list }}
    </div>
    
</template>

<script>
import Recipes from '../assets/Recipes.json';

import { eventBus } from '../event-bus';

export default {
    data ()  {
        return {
            meal_plan: {},
            meal_plan_summary: Array,
            shopping_list: []
        }
    },
    methods: {
       check_click: function(item){
           console.log("hey" + item);
           let index = this.shopping_list.findIndex(x => x.item == item);
           this.shopping_list[index].checked = true;
       },
       shopping_list_builder: function(meal_object){
   
    
        /* Code from V2  */   
        let days = ['monday','tuesday','wednesday','thursday','friday','saturday','sunday'];
        let times = ['lunch','dinner'];
        let meals = []
        let list = []
        let meal_plan = meal_object
        let ingredients

//THIS SECTION OF CODE IS SETTING UNDEFINED MEALS. IT IS BAD AND I SHOULD HAVE RE-WRITTEN IT FROM THE START

        // Going through every planned meal
        days.forEach(day => {
            times.forEach(time => {
                let r = meal_plan[day][time].recipe;
                let s = meal_plan[day][time].serves;

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
                        let i = meals.findIndex(indexOf)
                        let key = Object.keys(meals[i])
                        meals[i][key] += Number(s)
                    }
                }
            })
        })

        console.log(meals)
        /* End code from V2 */

        for (var meal in meals) {
            let recipe = Object.keys(meals[meal])
            let serves = Object.values(meals[meal])

            let index = Recipes.findIndex(x => x.recipe == recipe[0]);
            ingredients = Recipes[index].ingredients;
            for (var item in ingredients){
                let measure = (ingredients[item].amount/Recipes[index].serves)*serves;

                let list_item = {
                                item: ingredients[item].name,
                                amount: measure,
                                type: ingredients[item].type,
                                checked: false,
                                removed: false
                            }
                    if (list.findIndex(x => x.item == ingredients[item].name) == -1){
                                list.push(list_item)
                            } else {
                            let i = list.findIndex(x => x.item == ingredients[item].name)    
                            list[i]['amount'] += Number(measure)
                            }
                    }  
             }

    return list;

       }
    },
    mounted () {
        console.log("mounted");
        if (localStorage.meal_plan) {
            this.meal_plan = JSON.parse(localStorage.getItem('meal_plan'));
            }
        if (localStorage.shopping_list) {
          this.shopping_list = JSON.parse(localStorage.getItem('shopping_list'));
          } else {
          this.shopping_list = this.shopping_list_builder(this.meal_plan);
         }  
        eventBus.$on('meal_plan_edit', new_meal_plan => {
            this.shopping_list = this.shopping_list_builder(new_meal_plan);
                    });   
        eventBus.$on('meal_plan_edit', new_meal_plan => {
            this.meal_plan_summary = this.meal_plan_summary_builder();
            console.log(new_meal_plan);
                    }); 
        eventBus.$on('meal_plan_summary_change', new_summary => {
            console.log("the new summary");
            console.log(new_summary);
            //the new summary is being passed to the shopping list whenever a change is made to the meal plan!
            //now this needs to check if there is currently a meal_plan_summary present in this component
            //if there is, it needs to check itself against it 
            //Only then may you delete the V2 code from above and bring in a single shopping list that is built 
            // Ooooor, maybe in this event, I call a function to build a shopping list, then grab the current shopping 
                //list and check that with the new one.  – yep, I think that will work.

        }); 
    },
  watch: { 
        'shopping_list': { 
            handler: function() {
                localStorage.setItem('shopping_list', JSON.stringify(this.shopping_list)) 
            },
            deep: true,
            },
  },
}
</script>


<style scoped>

ul {
    list-style-type:none;
    padding: 0px 0px;
}

/* Start of custom checkbox styling */
input.checkbox {
    margin: 0px 10px 0px 0px;
    -webkit-appearance: none;
	background-color: #fafafa;
	border: 1px solid #cacece;
	padding: 9px;
	border-radius: 10px;
	display: inline-block;
    position: relative;
    top: 5px;
}

input.checkbox:active, input.checkbox:checked:active {
    box-shadow: 0 1px 2px rgba(0,0,0,0.05), inset 0px 1px 3px rgba(0,0,0,0.1);
}

input.checkbox:checked {
    background-color: #e9ecee;
	border: 1px solid #adb8c0;
	color: #99a1a7;
}

input.checkbox:checked:after {
    content: url(../assets/icons/check.png);
    transform: scale(.3);
	position: absolute;
	top: -8px;
	left: -12px;
	color: #99a1a7;
}
input.checkbox:focus {
    outline: none;
}
/* End of custom checkbox styling */

</style>