<template>
  <div class="todo-container">
    <div class="todo-wrap">
      <TodoHeader @addTodo="addTodo"/>
      <TodoList :todos="todos" :deleteTodo="deleteTodo"/>
      <todo-footer :todos="todos" :deleteCompleteTodos="deleteCompleteTodos" :selectAllTodos="selectAllTodos"/>
      <todo-footer>
        <input type="checkbox" v-model="isAllCheck" slot="checkAll"/>
        <span slot="count">已完成{{completeSize}} / 全部{{todos.length}}</span>
        <button class="btn btn-danger" v-show="completeSize" @click="deleteCompleteTodos" slot="delete">清除已完成任务</button>
      </todo-footer>
    </div>
  </div>
</template>

<script>
  import TodoHeader from './components/TodoHeader';
  import TodoList from  './components/TodoList';
  import TodoFooter from  './components/TodoFooter';
  import storageUtil from './util/storageUtil';
    export default {
      name: "App",
      data () {
        return {
          todos:storageUtil.readTodos()
        }
      },
      computed: {
        completeSize () {
          return this.todos.reduce((preTotal,todo) => preTotal + (todo.complete?1:0),0);
        },
        isAllCheck: {
          get() {
            return this.completeSize===this.todos.length && this.todos.length > 0;
          },
          set(value) {
            this.selectAllTodos(value);
          }
        }
      },
      methods: {
        addTodo (todo) {
          this.todos.unshift(todo);
        },
        deleteTodo (index) {
          this.todos.splice(index, 1);
        },
        //删除所有选中的todo
        deleteCompleteTodos () {
          this.todos = this.todos.filter(todo => !todo.complete);
        },
        //全选\全不选
        selectAllTodos (check) {
          this.todos.forEach(todo => todo.complete = check);
        }
      },
      watch: {
        todos: {
          deep: true,
          // handler: function (value) {
          //   storageUtil.saveTodos(value)
          // }
          handler: storageUtil.saveTodos
        }
      },
      components: {
        TodoHeader,
        TodoList,
        TodoFooter
      }
    }
</script>

<style scoped>
  /*app*/
  .todo-container {
    width: 600px;
    margin: 0 auto;
  }
  .todo-container .todo-wrap {
    padding: 10px;
    border: 1px solid #ddd;
    border-radius: 5px;
  }
</style>
