<template>
  <div class="todo-app">
    <h1>Todo List dengan Axios & JSON Server</h1>
    <div class="add-form">
      <input v-model="newTodo" placeholder="Tambah todo baru..." />
      <button @click="addTodo">Tambah</button>
    </div>
    <ul>
      <li v-for="todo in todos" :key="todo.id">
        <input
          type="checkbox"
          v-model="todo.completed"
          @change="toggleCompleted(todo)"
        />
        <span :class="{ done: todo.completed }">{{ todo.title }}</span>
        <button @click="editTodo(todo)">Edit</button>
        <button @click="deleteTodo(todo.id)">Hapus</button>
      </li>
    </ul>

    <div v-if="editing" class="edit-form">
      <input v-model="editingTodo.title" />
      <button @click="updateTodo">Simpan</button>
      <button @click="cancelEdit">Batal</button>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from "vue";
import axios from "axios";

const todos = ref([]);
const newTodo = ref("");
const editing = ref(false);
const editingTodo = ref(null);

const fetchTodos = async () => {
  const response = await axios.get("http://localhost:3000/todos");
  todos.value = response.data;
};

const addTodo = async () => {
  if (!newTodo.value.trim()) return;
  const response = await axios.post("http://localhost:3000/todos", {
    title: newTodo.value,
    completed: false,
  });
  todos.value.push(response.data);
  newTodo.value = "";
};

const deleteTodo = async (id) => {
  await axios.delete(`http://localhost:3000/todos/${id}`);
  todos.value = todos.value.filter((todo) => todo.id !== id);
};

const editTodo = (todo) => {
  editing.value = true;
  editingTodo.value = { ...todo };
};

const updateTodo = async () => {
  await axios.put(`http://localhost:3000/todos/${editingTodo.value.id}`, editingTodo.value);
  const idx = todos.value.findIndex((t) => t.id === editingTodo.value.id);
  todos.value[idx] = { ...editingTodo.value };
  editing.value = false;
};

const cancelEdit = () => {
  editing.value = false;
  editingTodo.value = null;
};

const toggleCompleted = async (todo) => {
  await axios.patch(`http://localhost:3000/todos/${todo.id}`, {
    completed: todo.completed,
  });
};

onMounted(fetchTodos);
</script>

<style scoped>
.todo-app {
  max-width: 600px;
  margin: 2rem auto;
  padding: 2rem;
  border-radius: 16px;
  background: #ffffff;
  box-shadow: 0 12px 32px rgba(0, 0, 0, 0.15);
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
  transition: transform 0.3s ease;
}

.todo-app:hover {
  transform: scale(1.01);
}

h1 {
  text-align: center;
  background: linear-gradient(45deg, #3498db, #2ecc71);
  -webkit-background-clip: text;
  color: transparent;
  font-weight: bold;
  font-size: 1.8rem;
  margin-bottom: 1rem;
}

.add-form,
.edit-form {
  display: flex;
  gap: 10px;
  margin-bottom: 1rem;
}

.add-form input,
.edit-form input {
  flex: 1;
  padding: 12px;
  border: 1px solid #ccc;
  border-radius: 8px;
  background-color: #f9f9f9;
  color: #333;
  font-size: 1rem;
  outline: none;
  transition: border-color 0.3s, box-shadow 0.3s;
}

.add-form input::placeholder,
.edit-form input::placeholder {
  color: #888;
}

.add-form input:focus,
.edit-form input:focus {
  border-color: #3498db;
  box-shadow: 0 0 6px rgba(52, 152, 219, 0.4);
}

button {
  background-color: #3498db;
  color: white;
  border: none;
  padding: 10px 16px;
  border-radius: 8px;
  cursor: pointer;
  transition: all 0.3s ease;
}

button:hover {
  background-color: #2980b9;
  transform: scale(1.05);
}

ul {
  list-style: none;
  padding: 0;
}

li {
  background: #f5f5f5;
  border-radius: 10px;
  padding: 12px 16px;
  margin-bottom: 10px;
  display: flex;
  align-items: center;
  justify-content: space-between;
  transition: transform 0.3s, background-color 0.3s;
  animation: fadeIn 0.4s ease;
}

li:hover {
  background-color: #e0e0e0;
  transform: translateX(5px);
}

li span {
  flex: 1;
  margin-left: 12px;
  word-break: break-word;
  color: #333;
}

.done {
  text-decoration: line-through;
  color: #999;
}

li input[type="checkbox"] {
  width: 18px;
  height: 18px;
  accent-color: #3498db;
}

li button {
  background-color: #27ae60;
  margin-left: 5px;
}

li button:nth-of-type(2) {
  background-color: #e74c3c;
}

li button:hover {
  filter: brightness(0.9);
}

.edit-form button {
  background-color: #f39c12;
}

.edit-form button:nth-of-type(2) {
  background-color: #95a5a6;
}

@keyframes fadeIn {
  from {
    opacity: 0;
    transform: translateY(-10px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

/* Responsive design */
@media (max-width: 600px) {
  .todo-app {
    padding: 1rem;
  }
  .add-form,
  .edit-form {
    flex-direction: column;
  }
  button {
    width: 100%;
  }
}
</style>
