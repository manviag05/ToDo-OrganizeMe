<template>
  <div  class="parent">
    <h2 class="primary">Better way to track your progress</h2>
    <h2  class="secondary">Add tasks to Complete</h2>
    <div>
      <input class="entry" type="text" v-model="newItem" placeholder="Enter new item...">
      <div  class="priority">
        <label class="modal-label"><input type="radio" v-model="selectedPriority" value="high">High Priority</label>
        <label class="modal-label"><input type="radio" v-model="selectedPriority" value="medium">Medium Priority</label>
        <label class="modal-label"><input type="radio" v-model="selectedPriority" value="low">Low Priority</label>
      </div>
      <button @click="addItem"  class="primary_btn">Add Item</button>
    </div>
    
    <div>
      <h2 class="secondary_w">Filter</h2>
      <label class="modal-label" id="para1"><input type="radio" v-model="filterPriority" value="all">All</label>
      <label class="modal-label" id="para1"><input type="radio" v-model="filterPriority" value="high">High Priority</label>
      <label class="modal-label" id="para1"><input type="radio" v-model="filterPriority" value="medium">Medium Priority</label>
      <label class="modal-label" id="para1"><input type="radio" v-model="filterPriority" value="low">Low Priority</label>
    </div>

    
      <div v-for="(item, index) in filteredItems" :key="index">
  <input type="checkbox" :id="'checkbox-' + index" :checked="item.checked" @click="toggleItem(index)"><span class="imp_text">{{ item.text }}</span>
  <label  :for="'checkbox-' + index"></label>
  <input type="text" v-model="item.text" v-if="item.editMode"> 
  <button class="edit" @click="enableEditMode(index)">  <img src="../assets/icons9-delete-button-30.png" alt="Edit" style="width: 20px; height: 20px;">{{ item.editMode ? 'Save' : 'Edit' }}</button> 
  <button class="edit" v-if="!item.editMode" @click="deleteItem(index)"> <img src="../assets/icons8-delete-button-30.png" alt="Delete" style="width: 20px; height: 20px;">Delete</button> 

    </div>
  </div>
</template>

<script>
import axios from 'axios';
export default {
  name: 'HelloWorld',
  data() {
    return {
      items: [],
      newItem: '',
      selectedPriority: '', // Variable to store selected priority
      filterPriority: 'all'
    }
  },

  computed: {
    filteredItems() {
      if (this.filterPriority === "all") {
        return this.items;
      } else {
        return this.items.filter(item => item.priority === this.filterPriority);
      }
    }
  },

  methods: {
    addItem() {
      if (this.newItem.trim() !== '') {
        const currentDate = new Date();
        const formattedDate = `${currentDate.getFullYear()}/${currentDate.getMonth() + 1}/${currentDate.getDate()}`;
        const currentTime = `${currentDate.getHours()}hr ${currentDate.getMinutes()}min ${currentDate.getSeconds()}sec`;
        
        let color = '';
        switch (this.selectedPriority) {
          case 'high':
            color = 'red';
            break;
          case 'medium':
            color = 'blue';
            break;
          case 'low':
            color = 'green';
            break;
          default:
            color = 'black';
        }

        this.items.push({  
          text: `${this.newItem} - ${formattedDate} --- ${currentTime}`, 
          checked: false, 
          editMode: false, 
          priority: this.selectedPriority,
          priorityColor: color  // Corrected property name
        });
        this.saveItems();
        this.newItem = '';
        this.selectedPriority = ''; // Reset selectedPriority after adding the item
      }

      const newItem = { 
      text: `${this.newItem}`, 
      checked: false, 
      editMode: false, 
      priority: this.selectedPriority,
    
    };

      axios.post('http://localhost:3001/items', newItem)
      .then(response => {
        console.log('Item added successfully:', response.data);
        // Optionally, you can update your local data with the response data if needed
        // Example:
        // this.items.push(response.data);
        // Reset input fields after adding the item
        this.newItem = '';
        this.selectedPriority = '';
      })
      .catch(error => {
        console.error('Error adding item:', error);
      });
    },
  
    toggleItem(index) {
      this.items[index].checked = !this.items[index].checked;
      this.saveItems();
    },
    enableEditMode(index) {
      this.items[index].editMode = !this.items[index].editMode;
    },
    updateItem(index) {
      this.items[index].editMode = false;
      this.saveItems();
    },
    deleteItem(index) { // Method to delete an item
      if (confirm("Are you sure you want to delete this item?")) {
        this.items.splice(index, 1);
        this.saveItems();
      }
    },
    saveItems() {
      localStorage.setItem('items', JSON.stringify(this.items))
    },
    loadItems() {
      const savedItems = localStorage.getItem('items');
      if (savedItems) {
        this.items = JSON.parse(savedItems);
      }
    }
  },

  mounted() {
    this.loadItems();
  }
}
</script>


<style>
.parent{
  background-image: linear-gradient(to bottom, #fff, #000);

}

.primary{
    margin-bottom: 2rem;
    font-size: 2.5rem;
    font-weight: 900;
}

.secondary{
  margin-bottom: 2rem;
    font-size: 2rem;
    font-weight: 900;
}

.entry{
  border-radius: 2rem;
  padding: 1rem 7rem 1rem 2rem ;
  border: 1px solid #ccc;
  margin-bottom: 1rem;

}

.primary_btn{
  padding: 1.2rem 3rem ;
  border-radius: 2rem;
  text-decoration:solid;
  background-image: linear-gradient(to right, aqua, #a90808);
  border: 1px solid ;
  border-image: linear-gradient(to right,grey);
  box-shadow: 2px 2px 5px #000;
}
.priority{
 margin: 0 0 2rem 0;
}

 input[type="radio"] {
  display: none;
}


.modal-label{
    display: inline-block;
    line-height: 2.2em;
    padding: 0 0.62em;
    border: 1px solid #666;
    border-radius: 0.25em;
    background-image: linear-gradient( to bottom, #fff, #ccc );
    box-shadow: inset 0 0 0.1em #fff, 0.2em 0.2em 0.2em rgba( 0, 0, 0, 0.3 );
    font-family: arial, sans-serif;
    font-size: 0.8em;
    margin-left: 1rem;
    align-items: center;
 }

.modal-label:hover {
    border-color: #3c7fb1;
    background-image: linear-gradient( to bottom, #fff, #a9dbf6 );
 }

.modal-label:focus-within {
    padding: 0  0.56em 0 0.68em;
    padding: 0 0.68em; /* Adjust padding as needed */
    outline: none; 
    background-color: blue;
 }

 .modal-label[type="checkbox"]:checked +label:after {
  background-color: blue;
}

.secondary_w{
  margin-bottom: 1rem;
    font-size: 3rem;
    font-weight: 900;
    color: #fff;
   
}
#para1{
  padding: 1rem;
  margin-bottom: 2rem;
  color: #a90808;
  font-size: 1rem;
}
.imp_text{
  font-size: 1.5rem;
  padding: 1rem;
  font-weight: 900;
  color:rgba(141, 196, 70, 0.689);
}

.edit{
  padding: 0.5rem;
  margin: 0.5rem;
  border-radius: .5rem;
}

</style>