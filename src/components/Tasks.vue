<template>
    <div v-for="task in tasks" :key="task.id">
        <Task @toggle-reminder="$emit('toggle-reminder', task.id)" 
        @delete-task="$emit('delete-task', task.id)" :task="task"/>
    </div>
</template>

<script>
import Task from './Task'
export default {
    // eslint-disable-next-line vue/multi-word-component-names
    name: 'Tasks',
    props: {
        tasks: Array,
    },
    components: {
        Task
    },
    emits: ['delete-task', 'toggle-reminder'],
}
</script>
