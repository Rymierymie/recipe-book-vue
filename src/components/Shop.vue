<template>
    <div class="componentDiv">
        <h4 id="headline">Shop
        <!-- <button @click="edit_list()" class="customButton" id="editListButton">Edit List</button> -->
        <img @click="edit_list()" class="icon-big" src="../assets/icons/edit.png" id="editListButton" />
        </h4>
        <ul> 
            <li v-for="(item, index) in shopping_list" v-bind:key="index" >
                <span v-if="item.amount != 0">
                    <input v-bind:id="item.item" type="checkbox" class="checkbox" @click="check_click(item.item)">
                    {{ item.item }} {{ parseFloat(item.amount.toFixed(2)) }} {{ item.type }}
                    <!-- <button style="display:none;" class="delete-button" @click="remove_item(item.item)">remove</button> -->
                    <img src="../assets/icons/cancel.png" class="icon-big display-toggle menuDeleteIcon delete-button" id="menuDeleteIcon" @click="remove_item(item.item)" />
                </span>
            </li>
        </ul>
<!--         <h3 v-if="custom_list.length !== 0">Custom items:</h3> -->
        <ul>
            <li v-for="(item, index) in custom_list" v-bind:key="index">
                <span class="custom-list-item">
                <input v-bind:id="item.item" type="checkbox" class="checkbox" @click="check_click_custom(item.item)">
                {{ item.item }}
                <!-- <button style="display:none;" class="delete-button" @click="remove_custom_item(item.item)">remove</button> -->
                <img src="../assets/icons/cancel.png" class="icon-big display-toggle menuDeleteIcon delete-button" id="menuDeleteIcon" @click="remove_custom_item(item.item)" />
                </span>
            </li>
        </ul>
        <div>
        <div id="add_to_list_input" class="display-toggle">
                <input type="text" id="custom_list_item" placeholder="Toilet paper">
                <img src="../assets/icons/plus.png" id="custom_list_add_button" class="icon-big" @click="custom_list_addition()"/>
        </div>

        <button @click="add_to_list_input()" class="customButton" id="addToListButton">Add To List</button>
        </div>
        <div style="padding-bottom: 100px;"></div>
    </div>
    
</template>

<script>
import Recipes from '../assets/Recipes.json';

import { eventBus } from '../event-bus';

export default {
    data ()  {

        return {
            shopping_list: [],
            custom_list: []
        }
    },
    methods: {
        custom_list_addition: function(){
            let custom_item_value = document.getElementById("custom_list_item").value;
            let custom_item = {
                item: custom_item_value,
                checked: false
            }
            console.log(custom_item);
            this.custom_list.push(custom_item);
            document.getElementById("custom_list_item").value = '';
        },
        add_to_list_input: function(){
            let elem = document.getElementById("add_to_list_input")
            console.log(elem);
            elem.classList.toggle("display-toggle");
            let input = document.getElementById("custom_list_item");
            console.log(input);
            input.addEventListener("keyup", function(event) {
                if (event.keyCode === 13) {
                event.preventDefault();
                document.getElementById("custom_list_add_button").click();
                }
            });  
        },
        remove_custom_item: function(item){
            let index = this.custom_list.findIndex(x => x.item == item);
            console.log(index)
            let list = this.custom_list;
            list.splice(index,1);

        },
        remove_item: function(item){
            let index = this.shopping_list.findIndex(x => x.item == item);
            console.log(index)
            this.shopping_list[index].removed = true;
            this.shopping_list[index].amount = 0;
        //TO DO - make the list able to parse a 'removed' item
        },

        edit_list: function(){
            //TO DO - add in icons for delete buttons
            let delete_buttons = document.getElementsByClassName('delete-button')
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
    check_click_custom: function(item){
           let index = this.custom_list.findIndex(x => x.item == item);
           if(this.custom_list[index].checked == true){
               this.custom_list[index].checked = false
           } else {
               this.custom_list[index].checked = true;
           }
       },
    check_click: function(item){
            let index = this.shopping_list.findIndex(x => x.item == item);
            if (this.shopping_list[index].checked == true){
                this.shopping_list[index].checked = false;
            } else {
                this.shopping_list[index].checked = true;
            }
            
       },
    shopping_list_builder: function(plan_summary){
            let list = []
            console.log("In the shopping list builder");
            for (var meal in plan_summary){
                let recipe = plan_summary[meal].recipe;
                let serves = plan_summary[meal].serves;
                let index = Recipes.findIndex(x => x.recipe == recipe);
                let ingredients = Recipes[index].ingredients;

            for (var item in ingredients){
                let measure = (ingredients[item].amount/Recipes[index].serves)*serves;
                let list_index = list.findIndex(x => x.item == ingredients[item].name)

                if (list_index == -1){
                    let list_item = {
                                item: ingredients[item].name,
                                amount: measure,
                                type: ingredients[item].type,
                                checked: false,
                                removed: false
                            }
                    list.push(list_item)
                } else {
                    list[list_index].amount = list[list_index].amount + measure;
                    }
                }
            }
            return list;
        }
    },
    created () {
  


        if (localStorage.shopping_list) {
          this.shopping_list = JSON.parse(localStorage.getItem('shopping_list'));
          console.log("Shopping list set from local storage")
          } else {
              console.log("No local storage to build a list from");
              localStorage.setItem('shopping_list', JSON.stringify(this.shopping_list))
         }  

        if (localStorage.custom_list) {
        this.custom_list = JSON.parse(localStorage.getItem('custom_list'));
        console.log("custom list set from local storage")
        } else {
            console.log("no local storage to build a list from");
            localStorage.setItem('custom_list', JSON.stringify(this.custom_list))
         } 

        eventBus.$on('meal_plan_summary_change', new_summary => {
            let component = this;
            let delete_buttons = document.getElementsByClassName('delete-button')
            for (let i = 0; i < delete_buttons.length; i++) {
                    delete_buttons.item(i).style.display = "none";
                    //document.getElementById("edit_list_button").src = "icons/edit.png";
                } 

            //Now checking if the new_list matches the current list
            let new_list = this.shopping_list_builder(new_summary);
            let new_list_length = new_list.length
            let current_list = JSON.parse(localStorage.getItem('shopping_list'));
            let current_list_length = current_list.length


            //if current list is empty, then set it to the new list
            if (current_list_length === 0){
                console.log("the original list is empty, so you can just assign the new list to being the current list!");
                component.shopping_list = new_list;
                localStorage.setItem('shopping_list', JSON.stringify(component.shopping_list));
            } else if (current_list_length > new_list_length){
                let placeholder_list = current_list.slice()
                 // if current list is longer than the new list, need to remove some items before returning the list
                console.log("the current list is longer, so we'll need to remove some items!");
                for (let list_item in current_list){
                    let index = new_list.findIndex(x => x.item == current_list[list_item].item);
                    if (index === -1){
                        let placeholder_index = placeholder_list.findIndex(x => x.item == current_list[list_item].item)
                        placeholder_list.splice(placeholder_index,1);
                    } else {
                        console.log("this items amount needs to be updated in the current list");
                        current_list[list_item].amount = new_list[index].amount;
                    }    
                }
                current_list = placeholder_list;
                component.shopping_list = current_list;
                localStorage.setItem('shopping_list', JSON.stringify(component.shopping_list))

            } else if (current_list_length < new_list_length){
                 // if the current list is shorter than the new list, need to add some items before updating the list
                console.log("the current list is shorter, so we'll need to add some items!");
                for (let item in new_list){
                    let index = current_list.findIndex(x => x.item == new_list[item].item);
                    if (index === -1){
                        console.log("this item is in the new list but not the current list - needs to be added!");
                        let list_item = {
                                item: new_list[item].item,
                                amount: new_list[item].amount,
                                type: new_list[item].type,
                                checked: false,
                                removed: false
                            }
                        current_list.push(list_item)
                    }
                    if (index != -1){
                        console.log("this item was already in the list, so we want to maintain any checked/removed meta it may have, but update the amount to the new amount");
                        current_list[index].amount = new_list[item].amount;
                    }
                }
                component.shopping_list = current_list;
                localStorage.setItem('shopping_list', JSON.stringify(component.shopping_list))

            } else if (current_list_length === new_list_length){
                // if the current list is the same length as the new list, we can assume no items have changed, so just need to update any amounts
                console.log("The new and current lists are the same length, so we'll just need to update the amounts on the current list");
                for (let item in current_list){
                    let index = new_list.findIndex(x => x.item == current_list[item].item);
                    current_list[item].amount = new_list[index].amount;
                }
                component.shopping_list = current_list;
                localStorage.setItem('shopping_list', JSON.stringify(component.shopping_list))
            }

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

  'custom_list': {
      handler: function() {
          localStorage.setItem('custom_list', JSON.stringify(this.custom_list)) 
          let list = this.custom_list; 
                for (let item in list){
                    if (list[item].checked == true){
                        console.log("this item is checked!");
                        let id = list[item].item;
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

      
  }
}
</script>


<style scoped>

ul {
    list-style-type:none;
    padding: 0px 0px;
    margin: 0px 0px;
    line-height: 2rem;
    font-size: 1.2rem;
}

.display-toggle {
    display: none;
}

.custom-list-item {
    color: #4897D8;
}

#editListButton {
    position: relative;
    float: right;
    top: 5px;
    margin-right: 20px;
}

#addToListButton {
    margin: 20px 0px 0px 30px;
}

input#custom_list_item {
    font-size: 1.2rem;
    margin: 10px 0px 0px 30px;
    padding: 5px 5px; 
    border-radius: 5px;
    outline: none;
    border-style: solid;
    border-width: 0.5px;
    border-color: rgba(0,0,0,0.1);
}

#custom_list_add_button {
    margin-left: 10px;
}

#headline {
    margin-bottom: 10px;
    margin-top: 15px;
    font-weight: 200;
}

.menuDeleteIcon {
    margin-left: 10px;
    position: relative;
    top: 2px;
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