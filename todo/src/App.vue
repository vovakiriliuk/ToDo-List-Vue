<script setup lang="ts">
import { ref, computed, watch, onMounted } from 'vue';

type Todo = {
  content: string;
  createdAt: number;
  done: boolean;
};

const todos = ref<Todo[]>([]);
const input_content = ref('');

const asc_todo = computed(() => {
  return [...todos.value].sort((a, b) => b.createdAt - a.createdAt);
});

const addTodo = () => {
  if (!input_content.value.trim()) return;

  todos.value.push({
    content: input_content.value,
    createdAt: Date.now(),
    done: false,
  });

  input_content.value = '';
};

const removeTodo = (createdAt: number) => {
  todos.value = todos.value.filter(t => t.createdAt !== createdAt);
};

watch(
    todos,
    (newValue) => {
      localStorage.setItem('todos', JSON.stringify(newValue));
    },
    { deep: true }
);

onMounted(() => {
  const saved = localStorage.getItem('todos');
  if (saved) {
    todos.value = JSON.parse(saved);
  }
});
</script>

<template>
  <main>
    <h2>To-do list</h2>

    <form @submit.prevent="addTodo">
      <section class="top">
        <input
            type="text"
            placeholder="Add a new task..."
            v-model="input_content"
            class="inputTop"
        />
        <input class="button" type="submit" value="Add" />
      </section>
    </form>

    <section class="todo-list">
      <div v-if="todos.length === 0" class="empty">
        <h4>Your todo list is empty</h4>
      </div>

      <div v-else>
        <h3>Tasks</h3>

        <div
            v-for="todo in asc_todo"
            :key="todo.createdAt"
            :class="['todo-item', { done: todo.done }]"
        >
          <input type="checkbox" v-model="todo.done" />

          <input
              type="text"
              v-model="todo.content"
              class="content"
          />

          <button
              class="delete"
              @click="removeTodo(todo.createdAt)"
          >
            ✕
          </button>
        </div>
      </div>
    </section>
  </main>
</template>

<style scoped>
main {
  background: #f9f9f9;
  padding: 16px;
  width: 320px;
  border-radius: 12px;
  box-shadow: 0 10px 25px rgba(0, 0, 0, 0.1);
}

h2 {
  text-align: center;
  margin-bottom: 12px;
}

.top {
  display: flex;
  gap: 8px;
}

.top input[type="text"] {
  flex: 1;
  padding: 8px;
  border-radius: 6px;
  border: 1px solid #ccc;
}

.button {
  border-radius: 8px;
  border: none;
  padding: 8px 14px;
  background: #0c1095;
  color: #fff;
  cursor: pointer;
  transition: 0.2s;
}

.button:hover {
  background: #0c9533;
}

.todo-list {
  margin-top: 16px;
}

.todo-item {
  display: flex;
  align-items: center;
  gap: 8px;
  background: #fff;
  padding: 8px;
  margin-bottom: 6px;
  border-radius: 8px;
}

.todo-item .content {
  flex: 1;
  border: none;
  background: transparent;
}

.todo-item.done .content {
  text-decoration: line-through;
  color: #999;
}

.delete {
  background: transparent;
  border: none;
  color: #e63946;
  font-size: 18px;
  cursor: pointer;
}

.empty {
  text-align: center;
  color: #777;
}

@media (prefers-color-scheme: dark) {
  .content {
    color: #000;
  }
  .inputTop {
    background: #f9f9f9;
    color: #fff;
  }

}
</style>
