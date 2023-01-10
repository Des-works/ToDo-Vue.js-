<template>
    <AddTask @add-task ="addTask"  v-show="showAddTask"/>
    <Tasks @toggle-reminder="toggleReminder"  @delete-task="deleteTask" :tasks="tasks"/>
</template>

<script>

import Tasks from '../components/Tasks.vue'
import AddTask from '../components/AddTask.vue'


export default {
  name: 'Home',
  props:{
    showAddTask: Boolean
  },
  components: {
    Tasks,
    AddTask,
  },
  data(){
    return {
      tasks: [],
    }
  },
  methods : {
    async addTask(task){
        
      const res = await fetch('api/tasks',{
        method: 'POST',
        headers : {
          'Content-type':'application/json'
        },
        body: JSON.stringify(task),
      })

      const data = await res.json()

      this.tasks = [...this.tasks, data]    
    },

    async deleteTask(id){
      if(confirm('Are you sure?')){

        const res = await fetch(`api/tasks/${id}`, {
          method: 'DELETE',

        })

        res.status === 200 ? (

        this.tasks = this.tasks.filter((task) => task.id !== id)) : alert('Error Deleting Task')
        
      }
      
    },
    async toggleReminder(id){
      const taskToToggle = await this.fetchTask(id)
      const updTask = {...taskToToggle, reminder: !taskToToggle.reminder}

      const res = await fetch(`api/tasks/${id}`,{
        method: 'PUT',
        headers:{
          'Content-type':'application/json',
        },
        body: JSON.stringify(updTask),
      })

      const data = await res.json()
      
      this.tasks = this.tasks.map((task) => 
      task.id === id ? {...task, reminder: data.reminder} : task)
      // console.log('it is working')
    },
    async fetchTasks() {
      const res = await fetch('api/tasks')

      const data = await res.json()
      return data
    },
    async fetchTask(id){
      const res = await fetch(`api/tasks/${id}`)

      const data = await res.json()
      return data
    }
  },
  async created(){
    // with db
    this.tasks = await this.fetchTasks()

    // hard coded
    // this.tasks = [
    //   {
    //     id : 1,
    //     text: 'Meeting with Schools',
    //     day: 'March 3rd at 2:39pm',
    //     reminder: true,
    //   },
    //   {
    //     id : 2,
    //     text: 'Reading time with littleone',
    //     day: 'March 7th at 7:10pm',
    //     reminder: true,
    //   },
    //   {
    //     id : 3,
    //     text: 'Gym section',
    //     day: 'March 30th at 9:00am',
    //     reminder: false,
    //   },
    // ]
  }
}


</script>