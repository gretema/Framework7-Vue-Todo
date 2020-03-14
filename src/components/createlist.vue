<template>
  <f7-block>
    <!-- List needs an id -->
    <f7-list no-hairlines-md id="list-form">
      <!-- List inputs need names -->
      <f7-list-input
        v-on:input="validateForm"
        name="listname"
        type="text"
        placeholder="清單名稱"
        clear-button
        required
        validate
      ></f7-list-input>
      <f7-list-input name="listitem" type="text"
        placeholder="需要完成什麼事？"></f7-list-input>
    </f7-list>

    <!-- Button to get inputs is outside the list -->
    <f7-row class="button-row">
      <f7-col>
        <f7-button style="max-width: 150px;" fill @click="addTask">新增任務</f7-button>
      </f7-col>
      <f7-col>
        <f7-button
          style="max-width: 150px;"
          raised
          @click="createList"
          :disabled="disabled == 1 ? true : false"
        >新增清單</f7-button>
      </f7-col>
    </f7-row>

    <!-- List preview here -->
    <!-- Render the list name -->
    <h2 id="list-name-preview">{{ listName }}</h2>
    <f7-list inset no-hairlines-md id="tasks-preview">
      <!-- Dynamically rendering the list items using v-for -->
      <f7-list-item id="list-item-preview" v-for="(listItem, index) in listItems" :key="index">
        {{ index+1 }}. {{ listItem.item }}
        <!-- Icon span with a @click to remove tasks -->
        <span @click="removeTodo(index)">
          <f7-icon f7="close" size="25px" color="red"></f7-icon>
        </span>
      </f7-list-item>
    </f7-list>
  </f7-block>
</template>

<script>
export default {
  data() {
    return {
      listName: "",
      listItems: [],
      listItem: {},
      createdList: {},
      lists: [],
      listid: 0,
      disabled: 1
    };
  },
  methods: {
    validateForm: function() {
      let formData = this.$f7.form.convertToData("#list-form");
      this.listName = formData.listname;
      if (formData.listname == "") {
        this.disabled = 1;
      } else if (this.listItems.length == 0) {
        this.disabled = 1;
      } else {
        this.disabled = 0;
      }
    },
    createList: function() {
      // Read user inputs
      // app.form.convertToData(form)- convert form fields values to data object
      let formData = this.$f7.form.convertToData("#list-form");
      if (
        formData.listname != "" &&
        formData.listname != undefined &&
        this.listItems.length != 0
      ) {
        // Store inputs in data properties
        this.createdList = {
          name: formData.listname,
          items: this.listItems,
          id: this.listid
        };
        // Reset form by emptying input fields with Dom7
        // .input-with-value - will be added to input when it has value
        this.$$("input.input-with-value").forEach(function(input) {
          input.value = "";
        });
        // Increment listid, and reset data properties used to store user input
        this.listid++;
        this.listItems = [];
        this.listName = "";
        this.disabled = 1;
        // Push created list into an array of lists
        this.lists.push(this.createdList);
        console.log(this.createdList);
        this.$emit('created', this.createdList);
        this.$f7.tab.show(tab1, true);
      } else {
        // If list has no name or no items, nothing happens
        return false;
      }
    },
    addTask: function() {
      // Again, read form inputs with Dom7
      let formData = this.$f7.form.convertToData("#list-form");
      // Form validation, check if the user input is empty or not
      if (formData.listitem != "" && formData.listitem != undefined) {
        // Store input in data property,
        // add a "done" property that is always false by default (marks whether task is done or not)
        this.listItem = {
          item: formData.listitem,
          done: false
        };
        // Push the list item to an array of items, validate form and reset the list item input
        this.listItems.push(this.listItem);
        this.validateForm();
        this.$$("input.input-with-value").forEach(function(input) {
          if (input.name === "listitem") {
            input.value = "";
          }
        });
      } else {
        // If list item input is empty, nothing happens
        return false;
      }
    },
    removeTodo(selectedItem) {
      this.listItems.splice(selectedItem, 1);
      // If the listItems array is empty, prevent list creation
      if (this.listItems.length <= 0) {
        this.disabled = 1;
      }
    }
  }
};
</script>

<style media="screen" scoped>
.button-row {
  padding: 0 30px;
  margin-top: -10px;
}

#warning-text {
  padding-left: 35px;
  margin-top: 5px;
  color: red;
  z-index: 1;
}

#list-name-preview {
  padding-left: 20px;
  padding-top: 10px;
  margin-bottom: -30px;
}

#tasks-preview {
  margin-left: 0;
}
</style>
