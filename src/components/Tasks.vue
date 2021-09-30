<template>
    <draggable
        v-model="tasks"
        group="tasks"
        @start="drag = true"
        @end="drag = false"
    >
        <div :key="task.id" v-for="task in tasks">
            <Task
                @dblclick="$emit('toggle-reminder', task.id)"
                @delete-task="$emit('delete-task', task.id)"
                :task="task"
            />
        </div>
    </draggable>
</template>

<script>
// import draggable from "vuedraggable";
import Task from "./Task.vue";

export default {
    name: "Tasks",
    props: {
        tasks: Array,
    },
    components: {
        Task,
        // draggable,
    },
    emits: ["delete-task", "toggle-reminder"],
};
</script>
