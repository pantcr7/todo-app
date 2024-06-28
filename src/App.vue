<template>
  <main class="h-screen w-screen flex justify-center items-center bg-gray-100">
    <div v-if="showOverlay" class="absolute w-[100vw] h-[100vh] bg-[rgba(0,0,0,0.77)] flex justify-center items-center ">
      <div class="w-[600px] bg-white rounded-xl p-8 relative flex flex-col">
        <textarea v-model.trim="newNote"  ref="noteTextArea" class="note-textarea rounded-sm bg-gray-500 text-slate-50 border-none px-2" name="noteTextArea" id="noteTextArea" cols="30" rows="10" placeholder="Write your note here...">
         
        </textarea>
        <p v-if="errorMsg" class="text-red-400">{{ errorMsg }}</p>
        <button @click="addNote" class="px-5 py-3 bg-slate-500 border-none text-slate-50  text-xl mt-4 rounded"> Add Note</button>
        <button @click="showOverlay=false"   class="px-5 py-3 bg-red-400 border-none text-slate-50  text-xl mt-4 rounded">Close</button>
      </div>
    </div>
    <div class="bg-white shadow-lg rounded-lg p-6 w-full max-w-[800px]">
      <header class="flex justify-between items-center mb-4">
      <h1 class="text-3xl font-bold text-gray-900">Notes</h1>
      <button @click="openOverlay"  class="bg-slate-700 text-slate-200 rounded-full w-10 h-10">
        +
      </button>
    </header>
    
      <div class="bg-gray-200 rounded-lg p-4 overflow-y-auto max-h-[500px]">
        <!-- Notes Container -->
        <div class="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 gap-4">
          <!-- Example cards (replace with your data) -->
          <div v-for="(note,index) in notes" :key="note.id" class="bg-white rounded-lg shadow-md p-4 w-[225px] h-[225px] flex flex-col justify-between"  :style="{ backgroundColor: note.backgroundColor }">
            <p :class="note.isCompleted ? 'line-through' : ''" class="break-words">{{ note.text }}</p>
            <div class="flex justify-between">
              <p class="text-[12px] font-semibold">{{ note.date }}</p>
              <button @click="deleteNote(index)">
                <img src="./assets/delete.png" alt="Icon Description" style="width: 24px; height: 24px;">
              </button>
              <button @click="compeleteTodo(index)" v-if="!note.isCompleted">
                <img src="./assets/completed.png" alt="Icon Description" style="width: 24px; height: 24px;">
              </button>

            </div>
           
          </div>

        </div>
      </div>
    </div>
  </main>
</template>

<script setup>
// Your script logic here
import { ref,onMounted } from "vue";
import { nextTick } from 'vue'
import { v4 as uuidv4 } from 'uuid';
const showOverlay = ref(false)
const noteTextArea = ref(null);
const newNote = ref("")

const notes =ref([]);


const errorMsg = ref("");
function  getRandomColor() {
  return "hsl(" + Math.random()*360+ ",100%,75%)"
  
}

const addNote = () =>{
  if(newNote.value.length < 10){
    return errorMsg.value = "Note needs to be at least 10 charactes long"
  }
  const id = uuidv4();
    notes.value.push({
      id: id,
      text:newNote.value,
      date: new Date().toDateString(),
      backgroundColor:getRandomColor(),
      isCompleted: false
  })
  showOverlay.value = false;
  newNote.value = "";
  errorMsg.value = "";
}

const openOverlay = () => {
  showOverlay.value = true;
  // Use nextTick to wait for DOM update before focusing
  // This ensures the element is rendered before focusing
  
  nextTick(() => {
    noteTextArea.value.focus();
  });
}

onMounted(() => {
  const defaultNote = {
    id: uuidv4(),
    text: 'Always Love Your Family',
    date: new Date().toDateString(),
    backgroundColor: getRandomColor(),
    isCompleted: false
  };
  notes.value.push(defaultNote);
});

const deleteNote = (index) =>{
  notes.value.splice(index, 1);
};

const compeleteTodo = (index) =>{
  notes.value[index].isCompleted = true;
}


</script>

<style scoped>
.note-textarea {
  @apply font-mono text-base; /* Apply Tailwind classes for monospaced font and base text size */
  line-height: 1.5; /* Adjust line height for readability */
}



</style>
