<template>
  <!-- Display a message if no list is selected (when listSelected is false) -->
  <f7-block v-if="!listSelected">
    <h2 id="no-list">尚未選取待辦清單。</h2>
  </f7-block>
  
  <f7-block v-else>
    <f7-row id="title-row">
      <f7-col width="60">
        <h2 id="list-title"> {{ activeList.name }} </h2>
      </f7-col>
      <!-- Button to delete list -->
      <f7-col width="40" style="width: 100px;" id="delete-btn">
        <f7-button raised fill @click="deleteList(activeList)">
          刪除清單
        </f7-button>
      </f7-col>
    </f7-row>

    <!-- First display unchecked tasks -->
    <f7-list inset no-hairlines-md id="tasks">
      <f7-list-item v-for="(uncheckedTask, index) in uncheckedTasks" :key="index"
        @click="checkTask(uncheckedTask)">
        <f7-icon f7="circle" size="25px"></f7-icon>
        <span style="width: 85%;">{{ uncheckedTask.item }}</span>
      </f7-list-item>

      <!-- Second display checked tasks -->
      <f7-list-item v-for="(checkedTask, index) in checkedTasks" :key="index"
        @click="checkTask(checkedTask)" class="checked">
        <f7-icon f7="check_round" size="25px" color="green"></f7-icon>
        <span style="width: 85%;">{{ checkedTask.item }}</span>
      </f7-list-item>
    </f7-list>

  </f7-block>
</template>

<script>
export default {
  data() {
    return {
      listSelected: false
    }
  },
  props: ['todoList', 'lists'],
  computed: {
    activeList: function() {
      return this.todoList;
    },
    listArray: function() {
      return this.lists;
    },
    uncheckedTasks: function() {
      return this.activeList.items.filter(function(item) {
        return !item.done;
      })
    },
    checkedTasks: function() {
      return this.activeList.items.filter(function(item) {
        return item.done;
      })
    },      
  },
  watch: {
    todoList: function(newList, oldList) {
      if (Object.keys(newList).length != 0 && newList.constructor === Object) {
        this.listSelected = true;
      }
    }
  },
  methods: {
    checkTask: function(task) {
      task.done = !task.done;
      this.$emit('checked', this.checkedTasks);
    },
    deleteList: function(list) {
      // Find the index of the list to delete
      // (so we do not delete lists with the same name, for example)
      let listToDelete = this.listArray.indexOf(list);
      this.listArray.splice(listToDelete, 1);
      // Go back to default view ("no list is selected")
      this.listSelected = false;
    }
  }
}
</script>

<style media="screen" scoped>
#list-title {
  padding: 0 20px;
  margin-top: 0;
}

#tasks {
  margin-left: 0;
}

.checked {
  color: #ababab;
}

#title-row {
  margin-bottom: -20px;
  margin-right: 20px;
}

#no-list {
  padding: 0 20px;
}
</style>
