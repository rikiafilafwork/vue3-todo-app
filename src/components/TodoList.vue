<script setup>
import { ref, defineProps, computed } from 'vue'

const emit = defineEmits(['edit', 'delete', 'doneEdit', 'cancelEdit', 'completeAll', 'removeCompleted'])

const props = defineProps({
    todos: Array,
    remaining: Number
})
const edited = ref()
const inputSearch = ref('')
const filteredTodos = computed(() => {
    const search = inputSearch.value
    return props.todos.filter((todo) => todo.title.toLowerCase().includes(search.toLowerCase()))
})
const editTodo = todo => {
    emit('edit', todo);
    edited.value = todo
};

const deleteTodo = todo => {
    emit('delete', todo);
};

const doneEdit = todo => {
    emit('doneEdit', todo);
    edited.value = null
};

const cancelEdit = todo => {
    emit('cancelEdit', todo);
    edited.value = null
};

const checkedAll = (e) => {
    emit('completeAll', e);
}

function removeSearch() {
    inputSearch.value = ''
}

function removeCompleted() {
    emit('removeCompleted')
}
</script>
<template>
    <div class="relative w-full mb-3">
        <input type="search"
            class="block w-full p-4 pl-10 text-sm text-gray-900 border border-gray-300 rounded-lg bg-gray-50 focus:ring-blue-500 focus:border-blue-500 dark:bg-gray-700 dark:border-gray-600 dark:placeholder-gray-400 dark:text-white dark:focus:ring-blue-500 dark:focus:border-blue-500"
            placeholder="Search task" @keyup.enter="searchTodos" v-model="inputSearch">
    </div>
    <div class="flex justify-end mb-3 flex-wrap" v-show="inputSearch !== ''">
        <button @click="removeSearch" class="font-medium text-blue-600 dark:text-blue-500 hover:underline">
            Clear Search
        </button>
    </div>
    <table class="w-full text-left text-sm text-gray-500 dark:text-gray-400">
        <thead class="bg-gray-50 text-xs uppercase text-gray-700 dark:bg-gray-700 dark:text-gray-400">
            <tr>
                <th class="px-4 py-4">
                    <input id="toggle-all-list" type="checkbox"
                        class="w-4 h-4 text-blue-600 bg-gray-100 border-gray-300 rounded focus:ring-blue-500 dark:focus:ring-blue-600 dark:ring-offset-gray-800 focus:ring-2 dark:bg-gray-700 dark:border-gray-600"
                        :checked="remaining === 0" @change="checkedAll">
                </th>
                <th class="px-4 py-4">Task</th>
                <th class="px-4 py-4">Action</th>
            </tr>
        </thead>
        <tbody>
            <tr v-for="todo in filteredTodos" :key="todo.id" class="border-b dark:border-gray-700"
                :class="{ 'line-through bg-green-500 transition ease-in delay-150': todo.completed }">
                <td class="px-4 py-3">
                    <input type="checkbox" v-model="todo.completed"
                        class="w-4 h-4 text-blue-600 bg-gray-100 border-gray-300 rounded focus:ring-blue-500 dark:focus:ring-blue-600 dark:ring-offset-gray-800 focus:ring-2 dark:bg-gray-700 dark:border-gray-600" />
                </td>
                <td class="px-4 py-3">
                    <input v-if="todo === edited"
                        class="bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg focus:ring-blue-500 focus:border-blue-500 block w-full p-2.5 dark:bg-gray-700 dark:border-gray-600 dark:placeholder-gray-400 dark:text-white dark:focus:ring-blue-500 dark:focus:border-blue-500"
                        type="text" v-model="todo.title" @vue:mounted="({ el }) => el.focus()" @blur="doneEdit(todo)"
                        @keyup.enter="doneEdit(todo)" @keyup.escape="cancelEdit(todo)" />
                    <template v-else>
                        {{ todo.title }}
                    </template>
                </td>
                <td class="flex items-center px-4 py-3">
                    <button @click="editTodo(todo)"
                        class="flex items-center justify-center rounded-lg bg-blue-700 px-4 py-2 text-sm font-medium text-white hover:bg-blue-800 focus:outline-none focus:ring-4 focus:ring-green-300 dark:bg-green-600 dark:hover:bg-green-700 dark:focus:ring-green-800 mr-2">Edit</button>
                    <button @click="deleteTodo(todo)"
                        class="flex items-center justify-center rounded-lg bg-red-700 px-4 py-2 text-sm font-medium text-white hover:bg-red-800 focus:outline-none focus:ring-4 focus:ring-green-300 dark:bg-green-600 dark:hover:bg-green-700 dark:focus:ring-green-800">Delete</button>
                </td>
            </tr>
        </tbody>
        <tfoot>
            <tr class="text-gray-900 dark:text-white">
                <td class="px-6 py-3">
                    <strong>{{ remaining }}</strong>
                    <span>{{ remaining === 1 ? ' item' : ' items' }} left</span>
                </td>
                <td class="px-6 py-3 text-end" colspan="2">
                    <button v-show="filteredTodos.length > remaining" @click="removeCompleted"
                        class="font-medium text-blue-600 dark:text-blue-500 hover:underline">
                        Clear Completed
                    </button>
                </td>
            </tr>
        </tfoot>
    </table>
</template>