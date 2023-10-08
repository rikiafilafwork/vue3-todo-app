<script setup>
import { ref, computed, watchEffect } from 'vue'
import TodoList from '../components/TodoList.vue'
import TodoFilter from '../components/TodoFilter.vue'

const STORAGE_KEY = 'vue-todoapp'

const filters = {
    all: (todos) => todos,
    active: (todos) => todos.filter((todo) => !todo.completed),
    completed: (todos) => todos.filter((todo) => todo.completed)
}

// state
const todos = ref(JSON.parse(localStorage.getItem(STORAGE_KEY) || '[]'))
const newTodo = ref('')
const visibility = ref('all')
const editedTodo = ref()
const classActive = 'text-blue-700 text-xs hover:text-white border border-blue-600 bg-white hover:bg-blue-700 focus:ring-4 focus:outline-none focus:ring-blue-300 rounded-full text-base font-medium px-5 py-2.5 text-center mr-3 mb-3 dark:border-blue-500 dark:text-blue-500 dark:hover:text-white dark:hover:bg-blue-500 dark:bg-gray-900 dark:focus:ring-blue-800'
const classDefault = 'text-gray-900 text-xs border border-white hover:border-gray-200 dark:border-gray-900 dark:bg-gray-900 dark:hover:border-gray-700 bg-white focus:ring-4 focus:outline-none focus:ring-gray-300 rounded-full text-base font-medium px-5 py-2.5 text-center mr-3 mb-3 dark:text-white dark:focus:ring-gray-800'

// derived state
const filteredTodos = computed(() => filters[visibility.value](todos.value))
const remaining = computed(() => filters.active(todos.value).length)

// handle routing
window.addEventListener('hashchange', onHashChange)
onHashChange()

// persist state
watchEffect(() => {
    localStorage.setItem(STORAGE_KEY, JSON.stringify(todos.value))
})

function toggleAll(e) {
    todos.value.forEach((todo) => (todo.completed = e.target.checked))
}

function addTodo() {
    const value = newTodo.value
    if (value) {
        todos.value.push({
            id: Date.now(),
            title: value,
            completed: false
        })
        newTodo.value = ''
    }
}

function removeTodo(todo) {
    todos.value.splice(todos.value.indexOf(todo), 1)
}

let beforeEditCache = ''
function editTodo(todo) {
    beforeEditCache = todo.title
    editedTodo.value = todo
}

function cancelEdit(todo) {
    editedTodo.value = null
    todo.title = beforeEditCache
}

function doneEdit(todo) {
    if (editedTodo.value) {
        editedTodo.value = null
        todo.title = todo.title.trim()
        if (!todo.title) removeTodo(todo)
    }
}

function removeCompleted() {
    todos.value = filters.active(todos.value)
}

function onHashChange() {
    const route = window.location.hash.replace(/#\/?/, '')
    if (filters[route]) {
        visibility.value = route
    } else {
        window.location.hash = ''
        visibility.value = 'all'
    }
}
</script>

<template>
    <div class="flex items-center justify-center py-4 md:py-8 flex-wrap">
        <div
            class="block max-w-xl p-6 bg-white border border-gray-200 rounded-lg shadow hover:bg-gray-100 dark:bg-gray-800 dark:border-gray-700 dark:hover:bg-gray-700">
            <h1
                class="mb-4 text-4xl font-extrabold leading-none tracking-tight text-gray-900 md:text-5xl lg:text-6xl dark:text-white">
                Todo App</h1>
            <div class="relative">
                <input type="text"
                    class="block w-full p-4 pl-10 text-sm text-gray-900 border border-gray-300 rounded-lg bg-gray-50 focus:ring-blue-500 focus:border-blue-500 dark:bg-gray-700 dark:border-gray-600 dark:placeholder-gray-400 dark:text-white dark:focus:ring-blue-500 dark:focus:border-blue-500"
                    placeholder="Create new task" @keyup.enter="addTodo" v-model="newTodo">
                <button type="button"
                    class="text-white absolute right-2.5 bottom-2.5 bg-blue-700 hover:bg-blue-800 focus:ring-4 focus:outline-none focus:ring-blue-300 font-medium rounded-lg text-sm px-4 py-2 dark:bg-blue-600 dark:hover:bg-blue-700 dark:focus:ring-blue-800"
                    @click="addTodo">Submit</button>
            </div>
            <TodoFilter :visibility="visibility" />
            <TodoList :todos="filteredTodos" :remaining="remaining" @edit="editTodo" @delete="removeTodo"
                @doneEdit="doneEdit" @cancelEdit="cancelEdit" @completeAll="toggleAll" @removeCompleted="removeCompleted" />
        </div>
    </div>
</template>