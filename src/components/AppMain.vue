<template>
    <div class="input-note-field">
        <input type="text" class="input-title" v-model="newNoteTitle" placeholder="Input New Title"><br>
        <textarea class="input-text" v-model="newNoteText" placeholder="Input New Text" cols="30" rows="10"></textarea><br>
        <button class="btn" @click="editNote(editNoteId)" v-if="isEditNote">OK</button>
        <button class="btn" @click="addNote" v-else>Add</button>
        <button class="btn" @click="cancelEdit" v-if="isEditNote">Cancel</button>
        <p v-if="showEmptyError" class="error">Empty Note Title or Text.</p>
    </div>

    <div class="note-list">
        <div class="note-item" v-for="note in noteList" :key="note.id">
            <div class="note-title">
                <h3>{{ note.title }}</h3>
            </div>
            <div class="note-text">
                <p>{{ note.text }}</p>
            </div>
            <button class="btn" @click="showEditNote(note.id)">Edit</button>
            <button class="btn" @click="deleteNote(note.id)">Delete</button>
        </div>
    </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';

// Set up reactive variables
const newNoteTitle = ref('');
const newNoteText = ref('');
const noteList = ref([]);
const isEditNote = ref(false);
const showEmptyError = ref(false);
let editNoteId = -1;
let originalNote = { title: '', text: '' };

// Save to local storage (common to multiple functions)
const saveToLocalStorage = (key, data) => {
    try {
        localStorage.setItem(key, JSON.stringify(data));
    } catch (error) {
        confirm(error);
    }
}

// Get data from local storage
const getFromLocalStorage = (key) => {
    const data = localStorage.getItem(key);
    return data ? JSON.parse(data) : [];
}

// Add new note
const addNote = () => {
    if (newNoteTitle.value.trim() === '' || newNoteText.value.trim() === '') {
        showEmptyError.value = true;
        return;
    }
    else {
        const id = Date.now();
        showEmptyError.value = false;
        noteList.value.push({ id: id, title: newNoteTitle.value.trim(), text: newNoteText.value.trim() });
        saveToLocalStorage('noteList', noteList.value);
        newNoteTitle.value = '';
        newNoteText.value = '';
    }
}

// Show edit note
const showEditNote = (id) => {
    const note = noteList.value.find(newNote => newNote.id === id);
    originalNote.title = note.title;
    originalNote.text = note.text;
    newNoteTitle.value = note.title;
    newNoteText.value = note.text;
    isEditNote.value = true;
    editNoteId = id;
}

// Edit note
const editNote = (id) => {
    const note = noteList.value.find(newNote => newNote.id === id);
    const index = noteList.value.indexOf(note);
    const updateNote = { id: id, title: newNoteTitle.value, text: newNoteText.value };
    noteList.value.splice(index, 1, updateNote);
    saveToLocalStorage('noteList', noteList.value);
    newNoteTitle.value = '';
    newNoteText.value = '';
    isEditNote.value = false;
}

// Cancel edit
const cancelEdit = () => {
    const note = noteList.value.find(newNote => newNote.id === editNoteId);
    note.title = originalNote.title; // Restore original title
    note.text = originalNote.text; // Restore original text
    newNoteTitle.value = '';
    newNoteText.value = '';
    isEditNote.value = false;
}

// Delete note
const deleteNote = (id) => {
    if (!confirm('Are you sure?')) {
        return;
    }
    noteList.value = noteList.value.filter(newNote => newNote.id !== id);
    saveToLocalStorage('noteList', noteList.value);
}

// Get "saved data" from local storage
onMounted(() => {
    const savedNotes = getFromLocalStorage('noteList');
    if (savedNotes) {
        noteList.value = savedNotes;
    }
});


</script>

<style scoped>
.input-note-field {
    margin-bottom: 2em;
}

.input-title {
    width: 95%;
    padding: 0.5em;
    margin-bottom: 1em;
}

.input-text {
    width: 95%;
    padding: 0.5em;
    margin-bottom: 1em;
}

.btn {
    padding: 0.5em 1em;
    margin-right: 1em;
    cursor: pointer;
}

.error {
    color: red;
}

.note-list {
    margin-top: 2em;
}

.note-item {
    border: 1px solid #ccc;
    padding: 1em;
    margin-bottom: 1em;
}

.note-title {
    font-weight: bold;
}

.note-text {
    margin-top: 1em;
}

</style>
