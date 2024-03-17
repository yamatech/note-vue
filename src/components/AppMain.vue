<template>
    <div class="input-field">
        <input type="text" class="input-text" v-model="newTodo" placeholder="Input New Todo">
        <button class="btn blue" @click="editTodo" v-show="isEditTodo">OK</button>
        <button class="btn" @click="addTodo" v-show="!isEditTodo">Add</button>
        <p v-if="showError" class="error">Nothing has been entered for Todo.</p>
    </div>

    <div class="todo-list">
        <div class="todo-item" v-for="todo in todoList" :key="todo.id">
            <input type="checkbox" class="todo-checkbox">
            <p>{{ todo.task }}</p>
            <button class="btn blue" @click="showEditTodo(todo.id)">Edit</button>
            <button class="btn red" @click="deleteTodo(todo.id)">Delete</button>
        </div>
    </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';

// Set up reactive variables
const newTodo = ref('');
const todoList = ref([]);
const isEditTodo = ref(false);
const showError = ref(false);
let editTodoId = -1;

// Save to local storage (common to multiple functions)
const saveToLocalStrage = (key, data) => {
    try {
        localStorage.setItem(key, JSON.stringify(data));
    } catch (error) {
        confirm(error);
    }
}

// Get data from local storage
const getFromLocalStrage = (key) => {
    const data = localStorage.getItem(key);
    return data ? JSON.parse(data) : [];
}

// Add new todo
const addTodo = () => {
    if (newTodo.value.trim() === '') {
        showError.value = true;
        return;
    }
    else {
        const id = Date.now();
        showError.value = false;
        todoList.value.push({ id: id, task: newTodo.value.trim() });
        saveToLocalStrage('todoList', todoList.value);
        newTodo.value = '';
    }
}

// Edit todo
const showEditTodo = (id) => {
    const todo = todoList.value.find(newTodo => newTodo.id === id);
    newTodo.value = todo.task;
    isEditTodo.value = true;
    editTodoId = id;
}

const editTodo = () => {
    const todo = todoList.value.find(newTodo => newTodo.id === editTodoId);
    const index = todoList.value.indexOf(todo);
    todo.task = newTodo.value;
    todoList.value[index] = todo;
    saveToLocalStrage('todoList', todoList.value);
    newTodo.value = '';
    isEditTodo.value = false;
}

// Delete todo
const deleteTodo = (id) => {
    if (!confirm('Are you sure?')) {
        return;
    }
    todoList.value = todoList.value.filter(newTodo => newTodo.id !== id);
    saveToLocalStrage('todoList', todoList.value);
}

// Get "saved data" from local storage
onMounted(() => {
    const savedTodos = getFromLocalStrage('todoList');
    if (savedTodos) {
        todoList.value = savedTodos;
    }
});
</script>

<style scoped>
/* Input field settings */
.input-field {
    width: 100%;
    height: 5em;
    margin: 0 auto;
    text-align: center;
}

.input-text {
    margin: 0;
    padding: 1em;
    border: 1px solid #ccc;
    border-radius: 10px;
    width: 50vw;
    text-align: left;
    font-size: 1em;
}

.error {
    color: red;
}

/* Button settings */
.btn {
    margin: 0 0.5em;
    padding: 1em;
    width: 5em;
    border: 1px solid #ccc;
    border-radius: 10px;
    background-color: lightseagreen;
    color: white;
    font-size: 0.8em;
    cursor: pointer;
}

.blue {
    background-color: rgb(50, 120, 230);
}

.red {
    background-color: rgb(230, 20, 20);
}


/* Todo list display settings */
.todo-list {
    margin-top: 1em;
    display: flex;
    flex-direction: column-reverse;
    align-items: center;
}

.todo-item {
    display: flex;
    align-items: center;
    margin: 0.5em;
}

.todo-item p {
    text-align: left;
    width: 50vw;
}

.todo-checkbox {
    border: 1px solid #555;
    margin: 0 0.5em;
}
</style>
