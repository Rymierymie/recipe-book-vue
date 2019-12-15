<template>
    <div>
        <h1 id="headline">Shop</h1>
        <button @click="edit_list()">Edit List</button>
        <ul> 
            <li v-for="(item, index) in shopping_list" v-bind:key="index" >
                <span v-if="item.amount != 0">
                    <input v-bind:id="item.item" type="checkbox" class="checkbox" @click="check_click(item.item)">
                    {{ item.item }} {{ item.amount }} {{ item.type }}
                    <button style="display:none;" class="delete-button" @click="remove_item(item.item)">remove</button>
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
            //meal_plan: {},
            //meal_plan_summary: Array,
            shopping_list: []
        }
    },
    methods: {
        remove_item: function(item){
            let index = this.shopping_list.findIndex(x => x.item == item);
            console.log(index)
            //Do I actually want to remove this item, or just declare that it has been removed. 
            //The item has to be able to return on a meal plan edit. I think it's easier just to remove it
            //And then scrap the 'removed' true/false from the item.
            let list = this.shopping_list;
            list.splice(index,1);

        },

        edit_list: function(){
        
        let delete_buttons = document.getElementsByClassName('delete-button')
        console.log(delete_buttons)

      /*   for (let elem in delete_buttons){
            delete_buttons[elem].classList.toggle("display-toggle");
        } */
       
        for (let i = 0; i < delete_buttons.length; i++) {
            if (delete_buttons.item(i).style.display == "inline"){
                
                delete_buttons.item(i).style.display = "none";
                //document.getElementById("edit_list_button").src = "icons/edit.png";
            } else {
                delete_buttons.item(i).style.display = "inline";
                //document.getElementById("edit_list_button").src = "icons/check.png";
            }
        } 
    },
       check_click: function(item){
           console.log("hey" + item);
           let index = this.shopping_list.findIndex(x => x.item == item);
           this.shopping_list[index].checked = true;
       },
/*        shop_list_difference_checker: function(long_list, short_list){

           for (let item in long_list
       }, */
       shop_list_builder_2: function(plan_summary){
           let list = []
           console.log("inside the shopping list builder");
           //console.log(plan_summary);
           for (var meal in plan_summary){
               let recipe = plan_summary[meal].recipe;
               let serves = plan_summary[meal].serves;
               //console.log(recipe + " & " + serves)

               let index = Recipes.findIndex(x => x.recipe == recipe);
               //console.log(index);

               let ingredients = Recipes[index].ingredients;
            for (var item in ingredients){
                let measure = (ingredients[item].amount/Recipes[index].serves)*serves;
                //console.log(measure);

                let list_index = list.findIndex(x => x.item == ingredients[item].name)
                /* console.log("the index is:")
                console.log(list_index);
                console.log(ingredients[item].name); */

                if (list_index == -1){
                    let list_item = {
                                item: ingredients[item].name,
                                amount: measure,
                                type: ingredients[item].type,
                                checked: false,
                                removed: false
                            }
                    //console.log(list_item);
                    list.push(list_item)
                } else {
                    list[list_index].amount = list[list_index].amount + measure;
                }

         /*        if (meals.findIndex(indexOf) == -1){
                        meals.push(meal)
                    } else {
                        let i = meals.findIndex(indexOf)
                        let key = Object.keys(meals[i])
                        meals[i][key] += Number(s)
                    } */


                
                }
               

           }
           console.log("the new list!")
           return list;
       }
    },
    created () {
        console.log("this is created!");

        /* if (localStorage.meal_plan) {
            this.meal_plan = JSON.parse(localStorage.getItem('meal_plan'));
            } */
        //let app = this;
        if (localStorage.shopping_list) {
          this.shopping_list = JSON.parse(localStorage.getItem('shopping_list'));
          console.log("shopping list set from local storage")

          } else {
              console.log("no local storage to build a list from");
              localStorage.setItem('shopping_list', JSON.stringify(this.shopping_list))
              
          //this.shopping_list = this.shopping_list_builder(this.meal_plan);
         }  
/*         eventBus.$on('meal_plan_edit', new_meal_plan => {
            this.shopping_list = this.shopping_list_builder(new_meal_plan);
                    });   */ 
        /* eventBus.$on('meal_plan_edit', new_meal_plan => {
            this.meal_plan_summary = this.meal_plan_summary_builder();
            console.log(new_meal_plan);
                    });  */
        eventBus.$on('meal_plan_summary_change', new_summary => {
            let component = this;
            let delete_buttons = document.getElementsByClassName('delete-button')
            console.log(delete_buttons)

            /*   for (let elem in delete_buttons){
                    delete_buttons[elem].classList.toggle("display-toggle");
                } */
            
                for (let i = 0; i < delete_buttons.length; i++) {
                        
                        delete_buttons.item(i).style.display = "none";
                        //document.getElementById("edit_list_button").src = "icons/edit.png";
                    } 

            console.log("the new summary");
            console.log(new_summary);
            console.log("the test");
            // The new summary passed in from the plan page is now going to be run through the shopping list builder
            //ONCE THE SHOPPING LIST BUILDER 2 function is working, need to turn off the eventbus for the old shopping list builder
                //and make sure that I can bring in a new plan and compare with the current BEFORE overriding.
            let new_list = this.shop_list_builder_2(new_summary);
            console.log(new_list);
            let new_list_length = new_list.length
            console.log("new list length")
            console.log(new_list_length)
            /* Use this to assign the correct meal_plan summary to the data for the page when needed
            component.meal_plan_summary = new_summary;
            console.log(component.meal_plan_summary); */

            //Now checking if the new_list matches the current list
            console.log("local storage shopping list");
            console.log(localStorage.shopping_list)
            let current_list = JSON.parse(localStorage.getItem('shopping_list'));
            console.log("the current list");
            console.log(current_list);
            let current_list_length = current_list.length
            console.log("current list length")
            console.log(current_list_length)
            //Interesting that these lists are already returning different values... Weird caching going on!

            //the new summary is being passed to the shopping list whenever a change is made to the meal plan!
            //now this needs to check if there is currently a meal_plan_summary present in this component
            //if there is, it needs to check itself against it 
            //Only then may you delete the V2 code from above and bring in a single shopping list that is built 
            // Ooooor, maybe in this event, I call a function to build a shopping list, then grab the current shopping 
                //list and check that with the new one.  – yep, I think that will work.


            //if current list is empty, then set it to the new list
            if (current_list_length === 0){
                console.log("the original list is empty, so you can just assign the new list to being the current list!");
                component.shopping_list = new_list;
                console.log("component shopping list:");
                console.log(component.shopping_list);
                localStorage.setItem('shopping_list', JSON.stringify(component.shopping_list))
                console.log("local storage shopping list");
                console.log(localStorage.shopping_list)
            } else if (current_list_length > new_list_length){
                let placeholder_list = current_list.slice()
                console.log("placeholder list");
                console.log(placeholder_list);
                 // if current list is longer than the new list, need to remove some items before returning the list
                console.log("the current list is longer, so we'll need to remove some items!");
                console.log(current_list);
                //let holder_list = current_list;
                /* let holder_list = current_list.slice();
                console.log("holder list");
                console.log(holder_list); */
                for (let list_item in current_list){
                    console.log("item! be a number")
                    console.log(list_item)
                    let index = new_list.findIndex(x => x.item == current_list[list_item].item);
                    console.log("the index of the item")
                    console.log(index)
                    if (index === -1){
                        console.log("this item needs to be removed. Index:");
                        let placeholder_index = placeholder_list.findIndex(x => x.item == current_list[list_item].item)
                        placeholder_list.splice(placeholder_index,1);
                        console.log(placeholder_list);
                        /* console.log(current_list[list_item].item);
                        current_list.splice(list_item,1);
                        console.log(JSON.stringify(current_list)) */
                    } else {
                        console.log("this items amount needs to be updated in the current list");
                        current_list[list_item].amount = new_list[index].amount;
                    }    
                }
                //current_list = holder_list;
                current_list = placeholder_list;
                component.shopping_list = current_list;
                console.log("component shopping list:");
                console.log(component.shopping_list);
                localStorage.setItem('shopping_list', JSON.stringify(component.shopping_list))
                console.log("local storage shopping list");
                console.log(localStorage.shopping_list)

            } else if (current_list_length < new_list_length){
                 // if the current list is shorter than the new list, need to add some items before updating the list
                console.log("the current list is shorter, so we'll need to add some items!");
                for (let item in new_list){
                    //console.log(new_list[item].item);
                    let index = current_list.findIndex(x => x.item == new_list[item].item);
                    //console.log(index);
                    if (index === -1){
                        console.log("this item is in the new list but not the current list - needs to be added!");
                        let list_item = {
                                item: new_list[item].item,
                                amount: new_list[item].amount,
                                type: new_list[item].type,
                                checked: false,
                                removed: false
                            }
                        /* console.log("list item to be added!");
                        console.log(list_item); */
                        current_list.push(list_item)
                    }
                    if (index != -1){
                        console.log("this item was already in the list, so we want to maintain any checked/removed meta it may have, but update the amount to the new amount");
                        current_list[index].amount = new_list[item].amount;
                    }
                }
                component.shopping_list = current_list;
/*                 console.log("component shopping list:");
                console.log(component.shopping_list); */
                localStorage.setItem('shopping_list', JSON.stringify(component.shopping_list))
                console.log("local storage shopping list");
                console.log(localStorage.shopping_list)

            } else if (current_list_length === new_list_length){
                // if the current list is the same length as the new list, we can assume no items have changed, so just need to update any amounts
                console.log("The new and current lists are the same length, so we'll just need to update the amounts on the current list");
                for (let item in current_list){
                    let index = new_list.findIndex(x => x.item == current_list[item].item);
                    current_list[item].amount = new_list[index].amount;
                }
                component.shopping_list = current_list;
                console.log("component shopping list:");
                console.log(component.shopping_list);
                localStorage.setItem('shopping_list', JSON.stringify(component.shopping_list))
                console.log("local storage shopping list");
                console.log(localStorage.shopping_list)
            }



        /*     //check if new_list and current_list match
            var x = current_list.map(function(item, index) {
                console.log("in them map function");
                console.log(item)
                console.log(new_list[index])
            // In this case item correspond to currentValue of array a, 
            // using index to get value from array b
            //return item - new_list[index];
            })
            console.log(x);
 */



        });


    },
    mounted () {
        console.log("mounted");
        

    },
  watch: { 
        'shopping_list': { 
            handler: function() {
                localStorage.setItem('shopping_list', JSON.stringify(this.shopping_list)) 

                //Try and use this code to update the checkboxes after the list has rendered
            let list = this.shopping_list; 
                for (let item in list){
                    if (list[item].checked == true){
                        console.log("this item is checked!");
                        let id = list[item].item;
                        //
                        console.log(id);
                        let selected = document.getElementById(id);
                        //`${id}`
                        console.log(selected);
                        selected.checked = true;
                    }
                }
                
                
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

.display-toggle {
    display: none;
}

/* Start of custom checkbox styling */
/* input.checkbox {
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
} */
/* End of custom checkbox styling */

</style>