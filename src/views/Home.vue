<template>
  <AddTask v-show="showAddTask" @add-task="addTask" />
  <Tasks
    @toggle-reminder="toggleReminder"
    @delete-task="deleteTask"
    :tasks="tasks"
  />
</template>

<script>
import Tasks from "../components/Tasks.vue";
import AddTask from "../components/AddTask.vue";
import axios from "axios"; // CORS issue, trying Axios out instead of fetch API

const apiBaseUrl = "http://127.0.0.1:8000/api";

export default {
  name: "Home",
  props: {
    showAddTask: Boolean,
  },
  components: {
    Tasks,
    AddTask,
  },
  data() {
    return {
      tasks: [],
    };
  },
  methods: {
    async addTask(task) {
      // Old method using fetch API
      // const res = await fetch(`${apiBaseUrl}/tasks`, {
      //   method: "POST",
      //   headers: {
      //     "Content-type": "application/json",
      //   },
      //   body: JSON.stringify(task),
      // });

      // const data = await res.json();

      // this.tasks = [...this.tasks, data];
      try {
        const res = await axios.post(`${apiBaseUrl}/tasks`, task);

        this.tasks = [...this.tasks, res.data];
      } catch (error) {
        console.log("Error Adding Task:", error);
      }
    },

    async deleteTask(id) {
      // Old method using fetch API
      // if (confirm("Are you sure?")) {
      //   const res = await fetch(`${apiBaseUrl}/tasks/${id}`, {
      //     method: "DELETE",
      //   });

      //   res.status === 200
      //     ? (this.tasks = this.tasks.filter((task) => task.id !== id))
      //     : alert("Error Deleting This Task");
      // }
      if (confirm("Are you sure?")) {
        try {
          const res = await axios.delete(`${apiBaseUrl}/tasks/${id}`);

          if (res.status === 200) {
            this.tasks = this.tasks.filter((task) => task.id !== id);
          } else {
            alert("Error Deleting This Task");
          }
        } catch (error) {
          console.log("Error Deleting Task:", error);
        }
      }
    },

    async toggleReminder(id) {
      // Old method using fetch API
      // const taskToToggle = await this.fetchTask(id);
      // const updTask = { ...taskToToggle, reminder: !taskToToggle.reminder };

      // const res = await fetch(`${apiBaseUrl}/tasks/${id}`, {
      //   method: "PUT",
      //   headers: {
      //     "Content-type": "application/json",
      //   },
      //   body: JSON.stringify(updTask),
      // });

      // const data = await res.json();

      // this.tasks = this.tasks.map((task) =>
      //   task.id === id ? { ...task, reminder: data.reminder } : task
      // );
      try {
        const taskToToggle = await this.fetchTask(id);
        const updTask = { ...taskToToggle, reminder: !taskToToggle.reminder };

        const res = await axios.put(`${apiBaseUrl}/tasks/${id}`, updTask);

        this.tasks = this.tasks.map((task) =>
          task.id === id ? { ...task, reminder: res.data.reminder } : task
        );
      } catch (error) {
        console.log("Error Toggling Reminder:", error);
      }
    },

    async fetchTasks() {
      // Old method using fetch API
      // const res = await fetch(`${apiBaseUrl}/tasks`);
      // const data = await res.json();
      // return data;
      try {
        const res = await axios.get(`${apiBaseUrl}/tasks`);

        return res.data;
      } catch (error) {
        console.error("Error Fetching Tasks:", error);
        return [];
      }
    },

    async fetchTask(id) {
      // Old method using fetch API
      // const res = await fetch(`${apiBaseUrl}/tasks/${id}`);
      // const data = await res.json();
      // return data;
      try {
        const res = await axios.get(`${apiBaseUrl}/tasks/${id}`);
        
        return res.data;
      } catch (error) {
        console.error("Error Fetching Task:", error);
        return null;
      }
    },
  },

  async created() {
    this.tasks = await this.fetchTasks();
  },
};
</script>
