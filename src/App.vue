<template>
    <div class="container">
        <Header
            @toggle-add-task="toggleAddTask"
            title="Task Tracker"
            :showAddTask="showAddTask"
        />
        <div v-if="showAddTask">
            <AddTask @add-task="addTask" />
        </div>
        <Tasks
            @toggle-reminder="toggleReminder"
            @delete-task="deleteTask"
            :tasks="tasks"
        />
    </div>
</template>

<script>
import Header from "./components/Header.vue";
import Tasks from "./components/Tasks.vue";
import AddTask from "./components/AddTask.vue";

const url = "http://localhost:5000";

export default {
    name: "App",
    components: {
        Header,
        Tasks,
        AddTask,
    },
    data() {
        return {
            tasks: [],
            showAddTask: false,
        };
    },
    methods: {
        toggleAddTask() {
            this.showAddTask = !this.showAddTask;
        },
        async fetchTasks() {
            const tasks = await fetch(`${url}/tasks`);
            const data = await tasks.json();

            return data;
        },
        async getTask(id) {
            const res = await fetch(`${url}/tasks/${id}`);
            const data = await res.json();
            return data;
        },
        async addTask(task) {
            task.order = this.tasks.length + 1;
            const res = await fetch(`${url}/tasks`, {
                method: "POST",
                headers: {
                    "Content-Type": "application/json",
                },
                body: JSON.stringify(task),
            });
            const body = await res.json();
            this.tasks = [...this.tasks, body];
        },
        async toggleReminder(id) {
            const taskToToggle = await this.getTask(id);
            const updatedTask = {
                ...taskToToggle,
                reminder: !taskToToggle.reminder,
            };
            const res = await fetch(`${url}/tasks/${id}`, {
                method: "PUT",
                headers: {
                    "Content-Type": "application/json",
                },
                body: JSON.stringify(updatedTask),
            });
            const data = await res.json();

            this.tasks = this.tasks.map((task) =>
                task.id === id ? { ...task, reminder: data.reminder } : task
            );
        },
        async deleteTask(id) {
            if (confirm("Are you sure?")) {
                const res = await fetch(`${url}/tasks/${id}`, {
                    method: "DELETE",
                });
                res.status === 200
                    ? (this.tasks = this.tasks.filter((task) => task.id !== id))
                    : alert("Error deleting task");
            }
        },
        async sortTasks() {
            this.tasks.sort((a, b) => a.order - b.order);
        },
    },
    async created() {
        this.tasks = await this.fetchTasks();
        this.sortTasks();
    },
};
</script>

<style>
@import url("https://fonts.googleapis.com/css2?family=Poppins:wght@300;400&display=swap");
* {
    box-sizing: border-box;
    margin: 0;
    padding: 0;
}
body {
    font-family: "Poppins", sans-serif;
}
.container {
    max-width: 500px;
    margin: 30px auto;
    overflow: auto;
    min-height: 300px;
    border: 1px solid steelblue;
    padding: 30px;
    border-radius: 5px;
}
.btn {
    display: inline-block;
    background: #000;
    color: #fff;
    border: none;
    padding: 10px 20px;
    margin: 5px;
    border-radius: 5px;
    cursor: pointer;
    text-decoration: none;
    font-size: 15px;
    font-family: inherit;
}
.btn:focus {
    outline: none;
}
.btn:active {
    transform: scale(0.98);
}
.btn-block {
    display: block;
    width: 100%;
}
</style>
