<script setup>
 import { ref, watch, onMounted } from 'vue'
 const title = ref('untitled')
 const content = ref('')
 const notes = ref([])
 const active = ref(false)
 const current_note = ref([])
 const count = ref(0)

 const addNote = () => {
  //  const rand = Math.floor(Math.random() * 500) + 1
    const rand = count.value
    notes.value.push({
      id:rand,
      title:title.value,
      content:content.value
    })

    current_note.value = notes.value.filter((note)=> {return note.id == rand})
    active.value = true
  console.log(current_note)
  count.value += 1
 }

 const setNote = (id) => {
   current_note.value = notes.value.filter((note) => { return note.id == id })
 }

 const removeNote = (id) => {
  notes.value = notes.value.filter((note) => {return note.id != id})
 }

watch(notes, (newNotes) => {
  localStorage.setItem('notes', JSON.stringify(newNotes));
  // You might also want to save the `count` to ensure ID uniqueness across sessions
  localStorage.setItem('noteCount', count.value.toString()); // Save count as string
}, { deep: true }); 

onMounted(() => {
  const storedNotes = localStorage.getItem('notes');
  const storedCount = localStorage.getItem('noteCount');

  if(storedNotes && storedCount){
    notes.value = JSON.parse(storedNotes);
    count.value = parseInt(storedCount, 10);
    current_note.value.push(notes.value[0])
    console.log(current_note.value)
    console.log(typeof(current_note.value))
    console.log('hey')
  }else{
    notes.value = []
    count.value = 0
    current_note.value = []
  }
})
</script>

<template>
  <div class="container">
    <aside>
      <div class="btn-con">
        <button @click="addNote" class=""><i class="fa-solid fa-plus"></i> Add Note</button>
      </div>
      <div>
        <ul>
          <li @click="setNote(note.id)" v-for="note in notes" :key="note.id">
            {{ note.title }}
            <span @click="removeNote(note.id)"><i class="fa-solid fa-trash"></i></span>
          </li>
        </ul>
      </div>
    </aside>
    <main>
      <div v-if="current_note.length > 0">
        <input type="text" v-model="current_note[0].title"><br>
        <textarea v-model="current_note[0].content"></textarea>
      </div>
    </main>
  </div>
</template>

<style>
body{
  padding:0;
  margin: 0;
}
.container{
  display: grid;
  grid-template-columns: 1fr 4fr;
  height: 100svh;
}
aside{
  background-color: #91C8E4;
}
main{
  background-color: #4682A9;
}
.btn-con{
  display: flex;
  justify-content: center;
  margin-block-start: 20px;
}
button{
      width: 80%;
      color: #fff;
      background-color: black;
      text-align: center;
      padding: 15px 0;
      cursor: pointer;
      font-size: 20px;
      border-radius: 10px;
}
button:hover{
  background-color: #595959;
}
input[type="text"]{
  padding:20px 10px;
  font-size: 30px;
  width: 100%;
  background: #4682A9;
  border-bottom: 2px solid #fff;
  color:white;
}
textarea{
  font-size: 30px;
  padding: 20px 10px;
  width: 100%;
  height: 100svh;
  color: white;
  background-color: #4682A9;
}
ul{
  padding-inline-start: 0
}
ul > li{
  text-decoration: none;
  list-style-type: none;
  display: flex; 
  justify-content: space-between;
  padding: 20px 10px;
  font-size:20px;
  background-color: #FFFBDE;
  margin-block-end: 10px;
  text-transform: capitalize;
  cursor: pointer;
}
</style>
