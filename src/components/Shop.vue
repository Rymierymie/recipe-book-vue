<template>
    <div class="componentDiv">
        <h3 class="headline pl10">Shop
        <!-- <button @click="edit_list()" class="c-button" id="editListButton">Edit List</button> -->
        <img @click="edit_list()" class="icon-big" src="../assets/icons/edit.png" id="editListButton" />
        </h3>
        <ul class="pb0 pl0 mb0 mt0"> 
            <template v-for="(item, index) in shopping_list">
            <span v-if="!pantryItems.includes(item.item)" v-bind:key="index">
            <span v-if="item.amount !== 0" v-bind:key="index">
            <li v-bind:key="index" class="card list-card">
                <!-- NEED TO FORMAT THESE -->
                    <input v-bind:id="item.item" type="checkbox" class="checkbox" @click="check_click(item.item)">
                    <h2 style="display:inline-block" class="mb0">{{ item.item.charAt(0).toUpperCase() + item.item.slice(1) }} </h2> 
                    <img src="../assets/icons/cancel.png" class="icon-big display-toggle menuDeleteIcon delete-button" id="menuDeleteIcon" @click="remove_item(item.item)" />
                    
                    <p style="position:relative; left: 30px;" class="mb0 mt0"> {{ parseFloat(item.amount.toFixed(2)) }} {{ item.type }}</p>
                    <!-- <button style="display:none;" class="delete-button" @click="remove_item(item.item)">remove</button> -->
                      
            </li>
            </span>
            </span>
            </template>
        </ul>
<!--         <h3 v-if="custom_list.length !== 0">Custom items:</h3> -->
        <ul class="pb0 pl0 mb0 mt0">
            <li v-for="(item, index) in custom_list" v-bind:key="index" class="card list-card">
                
                <input v-bind:id="item.item" type="checkbox" class="checkbox" @click="check_click_custom(item.item)">
                <h2 style="display:inline-block; margin-bottom:2px;" class="mb0 custom-list-item" >{{ item.item }}</h2>
                <!-- <button style="display:none;" class="delete-button" @click="remove_custom_item(item.item)">remove</button> -->
                <img src="../assets/icons/cancel.png" class="icon-big display-toggle menuDeleteIcon delete-button" id="menuDeleteIcon" @click="remove_custom_item(item.item)" />
                
            </li>
        </ul>
        <div>
        <div id="add_to_list_input" class="display-toggle card list-card">
                <input type="text" class="mr10" id="custom_list_item" placeholder="Toilet paper">
                <!-- decrease padding on these buttons to make fit in row on small decive -->
                <button @click="custom_list_addition()" id="custom_list_add_button" class="c-button c-button-fill mr10 pl10 pr10">Add</button>
                <button class="c-button pl10 pr10" style="float:right; top:10px;" @click="doneAddingCustom()">Done</button>
        </div>
          <div>
            <button v-if="!customListAddDisplay === true" @click="add_to_list_input()" class="c-button ml10">Add To List</button>
         </div>
        </div>
        <div class="card" id="pantry">
            <h3 class="headline-card" @click="showHidePantry()">Pantry 
                <img src="../assets/icons/chevron-down.png" class="icon-big viewPantryButton" id="pantry-chevron-down" />
                <img src="../assets/icons/chevron-up.png" class="icon-big viewPantryButton hidePantry" id="pantry-chevron-up" />
            </h3> 
            <p :class="pantryView" class="small-p">Items in the pantry list won't display in your shopping list.</p>
                <ul :class="pantryView" class="pb10 pl0 mb0 mt0">
                    <li v-for="(item, index) in pantryItems" v-bind:key="index" ><h2>{{ item }} <img src="../assets/icons/cancel.png" class="icon display-toggle pantry-delete-button" @click="removePantryItem(item)" /></h2></li>
                </ul>
                <button :class="pantryView" class="c-button" @click="editPantryList()" id="editPantryButton">Edit Pantry</button>
            <div id="ingredientSelectDiv" :class="pantryView">
                <h3 class="headline-card" >Add to pantry</h3>
                <select id="ingredients-list" >
                    <option selected></option>
                    <option v-for="(item, index) in ingredients_list" v-bind:key="index">
                        {{ item }}
                    </option>
                </select>
                <button class="c-button" @click="addToPantry()">Add item</button>
            </div>
        </div>
        <div style="padding-bottom: 100px;"></div>
    </div>
    
</template>

<script>
import Recipes from '../assets/Recipes.json';

import { eventBus } from '../event-bus';

export default {
    data ()  {

        
  let ingredients_list = [];
  for (var recipe in Recipes){
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

        return {
            shopping_list: [],
            ingredients_list: ingredients_list,
            custom_list: [],
            pantryItems: [],
            pantryView: 'hidePantry',
            chevron: '../assets/chevron-down.png',
            customListAddDisplay: false
        }
    },
    methods: {
        addToPantry: function(){
            // hiding any delete buttons if the user is currently editing the list
            let delete_buttons = document.getElementsByClassName('pantry-delete-button')
            for (let i = 0; i < delete_buttons.length; i++) {
                if (delete_buttons.item(i).style.display == "inline"){                
                    delete_buttons.item(i).style.display = "none";
                    //document.getElementById("edit_list_button").src = "icons/edit.png";
                }
            }
            let item = document.getElementById('ingredients-list').value
            console.log(item);
            let index = this.pantryItems.findIndex(x => x == item)
            console.log(index)
            if (index == -1){
              this.pantryItems.push(item);
              localStorage.setItem('pantryItems', JSON.stringify(this.pantryItems))
          } else {
              console.log("Already in the list!")
              }
        },
        showHidePantry: function(){
            if (this.pantryView === 'showPantry'){
                this.pantryView = 'hidePantry'
                document.getElementById("pantry-chevron-up").classList.add('hidePantry')
                document.getElementById("pantry-chevron-down").classList.remove('hidePantry')
                
            } else if (this.pantryView === 'hidePantry'){
                this.pantryView = 'showPantry'
                document.getElementById("pantry-chevron-down").classList.add('hidePantry')
                document.getElementById("pantry-chevron-up").classList.remove('hidePantry')

                //document.getElementById("viewPantryButton").src = "'../assets/icons/chevron-up.png'"
            }
        },
        custom_list_addition: function(){
            let custom_item_value = document.getElementById("custom_list_item").value;
            if (custom_item_value.length >= 3){
                let custom_item = {
                item: custom_item_value,
                checked: false
            }
            console.log(custom_item);
            this.custom_list.push(custom_item);
            document.getElementById("custom_list_item").value = '';
            } else {
                alert("Did you mean to add that?")
            }
            
        },
        doneAddingCustom: function(){
            let elem = document.getElementById("add_to_list_input")
            console.log(elem);
            elem.classList.toggle("display-toggle");

            if(this.customListAddDisplay === false){
                console.log(" = false, changing")
                this.customListAddDisplay = true;
            } else if(this.customListAddDisplay === true){
                console.log(" = true, changing")
                this.customListAddDisplay = false;
            }
        },
        add_to_list_input: function(){
            let elem = document.getElementById("add_to_list_input")
            console.log(elem);
            elem.classList.toggle("display-toggle");
            console.log(this.customListAddDisplay);

            if(this.customListAddDisplay === false){
                console.log(" = false, changing")
                this.customListAddDisplay = true;
            } else if(this.customListAddDisplay === true){
                console.log(" = true, changing")
                this.customListAddDisplay = false;
            }


    
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
        removePantryItem: function(item){
            let index = this.pantryItems.findIndex(x => x == item);
            console.log(index)
            this.pantryItems.splice(index, 1)
            localStorage.setItem('pantryItems', JSON.stringify(this.pantryItems))
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
    editPantryList: function(){
            //TO DO - add in icons for delete buttons
            let delete_buttons = document.getElementsByClassName('pantry-delete-button')
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
  
        if (localStorage.pantryItems) {
        this.pantryItems = JSON.parse(localStorage.getItem('pantryItems'));
        console.log("pantryItems list set from local storage")
        } else {
            console.log("no local storage to build a pantryItems from");
            localStorage.setItem('pantryItems', JSON.stringify(this.pantryItems))
         } 

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

input#custom_list_item {
    font-family: 'Poppins', sans-serif;
    color: #6D6D6D;
    font-size: 1rem;
    width: 45%;
    font-weight:200;
    padding: 9px 15px; 
    border-radius: 10px;
    outline: none;
    border-style: solid;
    border-width: 0.5px;
    border: solid 0.5px rgba(180, 194, 211, 0.2);
    margin-top: 5px;
    margin-bottom: 5px;
}


.headline {
    margin-bottom: 10px;
    margin-top: 15px;
    font-weight: 200;
}

.headline-card {
    font-weight: 200;
    margin-bottom: 0px;
    margin-top: 0px;
}

.menuDeleteIcon {
    margin-left: 42px;
    position: relative;
    float: right;
    top: 10px;
    right: 10px;
}

/* Start of custom checkbox styling */
input.checkbox {
    margin: 0px 10px 0px 0px;
    -webkit-appearance: none;
	background-color: #ffffff;
	border: solid 0.5px rgba(180, 194, 211, 0.2);
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
    background-color: #7DC092;
	border: solid 0.5px rgba(180, 194, 211, 0.2);
	color: #99a1a7;
}

input.checkbox:checked:after {
    content: url(../assets/icons/check.png);
    transform: scale(.3);
	position: absolute;
	top: -7px;
	left: -12px;
	color: #99a1a7;
}
input.checkbox:focus {
    outline: none;
}
/* End of custom checkbox styling */

.showPantry {
    display: block;
}

.hidePantry {
    display: none;
}

.viewPantryButton {
    position: relative;
    float: right;
    top: 5px;
    right: 5px;
}

.small-p {
    font-size: 0.8rem;
    font-weight: 200;
    margin-top: 0px;
    margin-bottom: 0px;
}


#editPantryButton {
    margin-bottom: 15px;
}

#ingredients-list {
    margin-bottom: 20px;
    margin-top: 10px;
}

.pantry-delete-button {
 float: right;
 top: 5px;
 right: 10px;
}

</style>