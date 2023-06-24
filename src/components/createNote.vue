<template>
  <div class="w-full mt-8 md:w-3/5 text-[#607b96] m-auto">
    <div v-if="notes.length > 0" class="flex flex-col gap-2">
      <div
        @click="showDescription(index)"
        class="bg-[#E5E9F0] cursor-pointer text-black px-3 py-2 rounded"
        v-for="(note, index) in notes"
        :key="index"
      >
        <div class="flex justify-between">
          <p class="font-bold text-xl">{{ note.title.toUpperCase() }}</p>

          <div class="flex gap-3">
            <button @click="handleEditNote(index)">
              <img :src="editIcon" class="w-[30px]" alt="" />
            </button>
            <button @click="handleDeleteModal(index)">
              <img :src="deleteIcon" class="w-[30px]" alt="" />
            </button>
          </div>
        </div>

        <p v-show="index === showItem">{{ note.description }}</p>
      </div>
    </div>
    <div v-else>
      <p>No notes yet.</p>
    </div>

    <div @click="showForm = !showForm" class= " mt-5 cursor-pointer">
      <img
        :src="showForm ? closeNote : addNotes"
        class="w-[50px] md:w-[100px] ml-auto"
        alt="add notes"
      />
    </div>

    <!-- // form for adding new note.  -->
    <form
      v-show="showForm"
      class="flex flex-col gap-3"
      action=""
      @submit.prevent="handleSubmit"
    >
      <div class="flex flex-col">
        <label for="">Title</label>
        <input
          class="border border-[#607b96] focus:ring-transparent before:border-none focus:ring-0 p-2 bg-[#011221] rounded-md focus:outline-[#43d9ad]"
          v-model="title"
          required
          type="text"
          placeholder="Title"
        />
      </div>
      <div class="flex flex-col">
        <label for="">Description</label>
        <textarea
          class="border p-2 focus:ring-0 bg-[#011221] rounded-md focus:outline-[#43d9ad] border-[#607b96]"
          v-model="description"
          @input="handleWordCount"
          required
          type="text"
          placeholder="Description"
        />
        <span v-show="wordCount">{{ wordCount }} words </span>
      </div>

      <button class="bg-[#1C2B3A] w-full rounded-md md:w-1/2 px-3 py-2 text-white">
        {{editingIndex === null ? 'Create' : 'Update' }}
      </button>
    </form>
<!-- // modal to confirm delete notes. -->
    <div
      v-show="showDeleteModal"
      class="fixed bg-black h-full flex items-center justify-center w-full inset-0 bg-opacity-40"
    >
      <div
        class="bg-[#1e2d3d] rounded w-[90%] md:w-1/2 items-center py-5 gap-5 flex flex-col"
      >
        <span>Confirm Delete ? </span>
        <div class="flex gap-2 w-[90%]">
          <button
            @click="showDeleteModal = false"
            class="bg-black w-1/2 rounded-sm px-2 py-1 text-white"
          >
            Cancel
          </button>
          <button
            @click="deleteNote"
            class="bg-red-600 w-1/2 text-white rounded-sm px-2 py-1"
          >
            Delete
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import closeNote from "../assets/icons8-close.svg";
import deleteIcon from "../assets/icons8-delete.svg";
import editIcon from "../assets/icons8-edit.svg";
import addNotes from "../assets/icons8-add-properties-100.png";
import { ref, watchEffect,onMounted } from "vue";

const showForm = ref(false);
const title = ref("");
const wordCount = ref("");
const showItem = ref(null);
const deleteItem = ref(null);
const editingIndex = ref(null)
const showDeleteModal = ref(false);
const description = ref("");
const noteItem = ref({
  title: title.value,
  description: description.value,
});
const notes =  ref([]);

watchEffect(() => {
  noteItem.value = {
    title: title.value,
    description: description.value,
  };
});


onMounted(()=>{
   const storedNotes = localStorage.getItem('notes')
   if (storedNotes) {
      notes.value = JSON.parse(storedNotes)
   }
})
function handleSubmit() {
  console.log(noteItem.value);

  if (editingIndex.value !== null  ) {
   notes.value.splice(0, 1, noteItem.value);
    editingIndex.value = null;
  } 
  else {
   notes.value.push(noteItem.value);
   notes.value.reverse();
  }
  title.value = "";
  description.value = "";
  wordCount.value = "";
  localStorage.setItem('notes',JSON.stringify(notes.value))
}
function showDescription(index) {
  showItem.value = index;
  console.log(showItem.value, index);
}
function handleWordCount() {
  const words = description.value.trim().split(/\s+/);
  wordCount.value = words.length;
  console.log(words);
}
function deleteNote() {
  console.log(deleteItem.value);
  let filteredNotes = notes.value.filter(
    (note, index) => index !== deleteItem.value
  );
  localStorage.setItem('notes',JSON.stringify(filteredNotes))
  setTimeout(() => {
    notes.value = filteredNotes;

    showDeleteModal.value = false;
  }, 100);
}
function handleDeleteModal(index) {
  showDeleteModal.value = true;
  deleteItem.value = index;
}
function handleEditNote(index) {
   showForm.value = true
   editingIndex.value = index
   let note  = notes.value[index]
   title.value = note.title
   description.value = note.description
}
</script>
