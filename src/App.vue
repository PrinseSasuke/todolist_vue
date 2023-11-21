<script setup>
import { ref } from "vue";

const showModal = ref(false);
const newNoteTitle = ref("");
const newTodoItem = ref("");
const errorMessage = ref("");
const notes = ref([]);

function getRandomColor() {
  return `hsl(${Math.random() * 360}, 100%, 75%)`;
}

const addNote = () => {
  if (newNoteTitle.value.length < 1 || newNoteTitle.value.trim() === "") {
    return errorMessage.value = "Note title cannot be empty";
  }

  notes.value.push({
    id: Math.floor(Math.random() * 1000000),
    title: newNoteTitle.value,
    todoList: [],
    date: new Date(),
    backgroundColor: getRandomColor()
  });

  showModal.value = false;
  newNoteTitle.value = "";
  errorMessage.value = "";
};

const addTodo = (noteId) => {
  const noteIndex = notes.value.findIndex((note) => note.id === noteId);
  if (newTodoItem.value.length < 1 || newTodoItem.value.trim() === "") {
    return errorMessage.value = "Todo item cannot be empty";
  }
  notes.value[noteIndex].todoList.push({
    text: newTodoItem.value,
    completed: false
  });
  newTodoItem.value = "";
  errorMessage.value = "";
};

const toggleTodo = (noteId, todoIndex) => {
  const noteIndex = notes.value.findIndex((note) => note.id === noteId);
  notes.value[noteIndex].todoList[todoIndex].completed = !notes.value[noteIndex].todoList[todoIndex].completed;
};
const editNote = (noteId) => {
    const noteIndex = notes.value.findIndex((note) => note.id === noteId);
    const newTitle = prompt("Enter a new title for the note:", notes.value[noteIndex].title);
    if (newTitle !== null && newTitle.trim() !== "") {
      notes.value[noteIndex].title = newTitle;
    }
  };

  const deleteNote = (noteId) => {
    const confirmDelete = confirm("Are you sure you want to delete this note?");
    if (confirmDelete) {
      notes.value = notes.value.filter((note) => note.id !== noteId);
    }
  };

</script>

<template>
  <main>
    <div v-if="showModal" class="overlay">
      <div class="modal">
        <input v-model.trim="newNoteTitle" type="text" placeholder="Note title">
        <p v-if="errorMessage">{{ errorMessage }}</p>
        <button @click="addNote()">Add note</button>
        <button class="close" @click="showModal = false">Close</button>
      </div>
    </div>
    <div class="container">
      <header>
        <h1>Notes</h1>
        <button @click="showModal = true">+</button>
      </header>
      <div class="cards-container">
        <div v-for="note in notes" :key="note.id" class="card" :style="{ backgroundColor: note.backgroundColor }">
          <h2>{{ note.title }}</h2>
          <!-- Todo list -->
          <div v-if="note.todoList.length > 0">
            <ul>
              <li v-for="(todo, index) in note.todoList" :key="index">
                <input type="checkbox" :id="`${note.id}-${index}`" :checked="todo.completed"
                  @change="toggleTodo(note.id, index)">
                <label :for="`${note.id}-${index}`" :class="{ completed: todo.completed }">{{ todo.text }}</label>
              </li>
            </ul>
          </div>
          <div v-else>
            <p>No todos yet.</p>
          </div>
          <!-- Add todo input -->
          <div>
            <input v-model.trim="newTodoItem" type="text" placeholder="Add todo">
            <button @click="addTodo(note.id)">Add</button>
          </div>
          <!-- Edit and delete buttons -->
          <div>
            <button @click="editNote(note.id)">Edit</button>
            <button @click="deleteNote(note.id)">Delete</button>
          </div>
          <p class="date">{{ note.date.toLocaleDateString("fr-EU") }}</p>
        </div>
      </div>
    </div>
  </main>
</template>

<style scoped>
main {
  height: 100vh;
  width: 100vw;
}

.container {
  max-width: 1000px;
  padding: 10px;
  margin: 0 auto;
}

header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

h1 {
  font-weight: bold;
  margin-bottom: 25px;
  font-size: 75px;
}

header button {
  border: none;
  padding: 10px;
  width: 50px;
  height: 50px;
  cursor: pointer;
  background-color: rgb(21, 20, 20);
  border-radius: 100%;
  color: white;
  font-size: 20px;
}

.card {
  width: 225px;
  height: 225px;
  background-color: rgb(237, 182, 44);
  padding: 10px;
  border-radius: 15px;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  margin-right: 20px;
  margin-bottom: 20px;
}

.date {
  font-size: 12.5px;
  font-weight: bold;
}

.cards-container {
  display: flex;
  flex-wrap: wrap;
}

.overlay {
  position: absolute;
  width: 100%;
  height: 100%;
  background-color: rgba(22, 21, 21, 0.77);
  z-index: 10;
  display: flex;
  justify-content: center;
  align-items: center;
}

.modal {
  width: 750px;
  background-color: #fff;
  border-radius: 10px;
  padding: 30px;
  position: relative;
  display: flex;
  flex-direction: column;
}

.modal button {
  padding: 10px;
  font-size: 20px;
  width: 100%;
  background-color: rgb(157, 64, 244);
  border: none;
  color: white;
  cursor: pointer;
  margin-top: 20px;

}

.modal .close {
  background-color: red;
  margin-top: 7px;
}

.modal p {
  color: tomato;
}
</style>