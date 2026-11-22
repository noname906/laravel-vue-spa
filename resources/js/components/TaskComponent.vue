<template>
    <div>
        <div class="flex felx-col gap-4 mb-6">
            <input v-model="form.title" type="text" placeholder="Enter title" class="border border-gray-300 rounded-lg p-3">
            <textarea v-model="form.description" placeholder="Enter description" class="border border-gray-300 rounded-lg p-3"></textarea>
            <button v-if="editMode" @click="updateTask" class="bg-yellow-500 text-white px-4 py-2 rounded-lg">Update Task</button>
            <button v-else @click="saveTask" class="bg-blue-500 text-white px-4 py-2 rounded-lg">Save Task</button>
        </div>
        <div v-for="task in tasks" :key="task.id">{{ task.title }}</div>
            <div v-for="task in tasks" :key="task.id" class="bg-gray-50 border rounded-lg p-5 mb-4 shadow-sm">
                <h2 class="text-2xl font-bold mb-2">
                    {{ task.title }}
                </h2>
                <p class="text-gray-600">
                    {{ task.description }}
                </p>
                <button @click="editTask(task)" class="text-yellow-600 mr-3">Edit</button>
<button @click="deleteTask(task.id)" class="text-red-600">Delete</button>
            </div>
    </div>
</template>

<script setup>
import axios from 'axios';
import { ref, onMounted } from 'vue';

const tasks = ref([]);
const pagination = ref({});
const editMode = ref(false);
const editId = ref(null);
const form = ref({ title: '', description: '' });

const getTask = async (page = 1) => {
    const response = await axios.get(`/api/tasks?page=${page}`);
    tasks.value = response.data.data;
    pagination.value = response.data;
};

const resetForm = () => {
    form.value = { title: '', description: '' };
    editMode.value = false;
    editId.value = null;
};

const saveTask = async () => {
    await axios.post('/api/tasks', form.value);
    resetForm();
    getTask();
};

const editTask = (task) => {
    form.value = { title: task.title, description: task.description };
    editId.value = task.id;
    editMode.value = true;
};

const updateTask = async () => {
    await axios.put(`/api/tasks/${editId.value}`, form.value);
    resetForm();
    getTask();
};

const deleteTask = async (id) => {
    await axios.delete(`/api/tasks/${id}`);
    getTask();
};

onMounted(() => getTask());
</script>
