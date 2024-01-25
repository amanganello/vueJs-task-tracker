<template>
    <AddTask @add-task="addTask" v-show="showAddTask"/>
    <Tasks @toggle-reminder="toggleReminder" 
      @delete-task="deleteTask" :tasks="tasks" />
</template>

<script>
import Tasks from '../components/Tasks';
import AddTask from '../components/AddTask.vue';

export default {
    // eslint-disable-next-line vue/multi-word-component-names
    name: 'Home',
    props: {
        showAddTask: Boolean,
    },
    components: {
        Tasks,
        AddTask
    },
    data() {
        return {
            tasks: [],
        }
    },
    methods: {
      async addTask(task) {
        const res = await fetch('api/tasks', {
          method: 'POST', 
          headers: {
            'Content-type': 'application/json',
          },
          body: JSON.stringify(task),
        });
        const data = await res.json();
        this.tasks = [...this.tasks, data];
      },
      async deleteTask(id) {
        if(confirm('Are you sure?')) {
          const res = await fetch(`api/tasks/${id}`, {
            method: 'DELETE', 
          });
          res.status === 200 
            ? (this.tasks = this.tasks.filter((task) => task.id !== id)) 
            : alert('Error deleting task');
        }
      },
      async toggleReminder(id) {
        const taskToToggle = await this.fetchTask(id);
        const updatedTask = {...taskToToggle, reminder: !taskToToggle.reminder}

        const res = await fetch(`api/tasks/${id}`, {
          method: 'PUT',
          headers: {
            'Content-type': 'application/json'
          },
          body: JSON.stringify(updatedTask)
        })
        res.status === 200 ?
        this.tasks = this.tasks.map((task) => 
          task.id === id ? {...task, reminder: !task.reminder} : task) 
          : alert('Error updating task');
      },
      async fetchTasks() {
        const res = await fetch('api/tasks');
        return await res.json();
      },
      async fetchTask(id) {
        const res = await fetch(`api/tasks/${id}`);
        return await res.json();
      }
  },
  async created() {
    this.tasks = await this.fetchTasks();
  },
}
</script>