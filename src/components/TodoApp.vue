<template>
  <div class="container">
    <h1>Hello World from TodoApp.vue</h1>
    <h2 class="text-center mt-5 mb-5">My Vue Todo App</h2>

    <div>
      <TestAPI></TestAPI>
    </div>

    <!--- Input -->
    <div class="row g-3 d-flex justify-content-center mb-5">
      <div
        class="alert alert-danger alert-dismissible fade show"
        role="alert"
        v-show="isVisible"
      >
        <span class="fa-solid fa-triangle-exclamation"></span>
        Please ensure all the inputs are filled in!
        <button
          type="button"
          class="btn-close"
          data-bs-dismiss="alert"
          aria-label="Close"
        ></button>
      </div>
      <div class="col-6 col-sm-6">
        <!-- v-model: 2-way binding -->
        <!-- in script tag, it is empty string but if we put value to it, it will appear in this input tag -->
        <input
          v-model="newTask"
          type="text"
          class="form-control"
          placeholder="Enter task"
        />
      </div>
      <div class="col-6 col-sm-6">
        <select
          v-model="selectedPriority"
          class="form-select"
          aria-label="Default select example"
        >
          <option disabled value selected>Select Priority Level</option>
          <option value="low">Low</option>
          <option value="medium">Medium</option>
          <option value="high">High</option>
          <option value="urgent">Urgent</option>
        </select>
      </div>
      <div class="col-2">
        <button type="button" @click="submitTask" class="btn btn-warning">
          SUBMIT
        </button>
      </div>
    </div>

    <table class="table table-bordered">
      <thead>
        <tr>
          <th scope="col">ID</th>
          <th scope="col">Task</th>
          <th scope="col">Status</th>
          <th scope="col">Priority Level</th>
          <th scope="col">#</th>
          <th scope="col">#</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(task, index) in dataApi" :key="index">
          <td>{{ task.id }}</td>
          <td>
            <span :class="{ finished: task.status === 'finished' }">
              {{ task.name }}
            </span>
          </td>
          <td style="width: 120px">
            <span
              class="pointer fw-bold"
              @click="changeStatus(index)"
              :class="{
                'text-danger': task.status === 'to-do',
                'text-warning': task.status === 'in-progress',
                'text-success': task.status === 'finished',
              }"
              >{{ firstCharUpper(task.status) }}</span
            >
          </td>
          <td>{{ firstCharUpper(task.priority) }}</td>
          <td>
            <div @click="editTask(index)">
              <span class="fa fa-pen"></span>
            </div>
          </td>
          <td>
            <div @click="deleteTask(index)">
              <span class="fa fa-trash"></span>
            </div>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
import TestAPI from "./TestAPI.vue";

const axios = require("axios");

export default {
  name: "TodoApp",
  components: {
    TestAPI,
  },

  data() {
    return {
      // to get value from input
      newTask: "",
      selectedPriority: "",
      editedTask: null,
      isVisible: false,
      availableStatus: ["to-do", "in-progress", "finished"],
      dataApi: null,

      tasks: [
        {
          id: 1,
          name: "Steal bananas from the supermarket.",
          status: "to-do",
          priority: "low",
        },
        {
          id: 2,
          name: "Eat 1 kg chocolate in 1 hour.",
          status: "in-progress",
          priority: "medium",
        },
        {
          id: 3,
          name: "Create YouTube video.",
          status: "finished",
          priority: "high",
        },
        {
          id: 4,
          name: "Create YouTube video.",
          status: "finished",
          priority: "urgent",
        },
      ],
    };
  },

  // display data when app is accessed
  mounted() {
    axios
      .get("http://todo-api.test/api/todo")
      .then((response) => {
        this.dataApi = response.data.todos;
      })
      .catch((error) => {
        console.log(error);
      });
  },

  methods: {
    submitTask() {
      // using v-model, we can get the value of the input tag
      // console.log(this.newTask)

      if (this.newTask.length === 0 || this.selectedPriority.length === 0) {
        this.isVisible = true;
        return;
      }

      if (this.editedTask === null) {
        this.tasks.push({
          id: Math.floor(Math.random() * 1001),
          name: this.newTask,
          status: "to-do",
          priority: this.selectedPriority,
        });

        axios
          .post("http://todo-api.test/api/todo", {
            name: this.newTask,
            status: "to-do",
            priority: this.selectedPriority,
          })
          .then((response) => {
            // this.$forceUpdate(); // tak jadi
            console.log(response);
          })
          .catch((error) => {
            console.log(error);
          });
      } else {
        // this.editedTask return the index of the array that need to be edited
        this.tasks[this.editedTask].name = this.newTask;
        this.tasks[this.editedTask].priority = this.selectedPriority;
        this.editedTask = null;
      }

      this.newTask = "";
      this.selectedPriority = "";

      this.$forceUpdate();
    },

    deleteTask(index) {
      // splice() - can add/remove items in array
      // at position index, remove 1 item
      this.tasks.splice(index, 1);
    },

    editTask(index) {
      this.newTask = this.tasks[index].name;
      this.selectedPriority = this.tasks[index].priority;
      this.editedTask = index;
    },

    changeStatus(index) {
      let newIndex = this.availableStatus.indexOf(this.tasks[index].status);

      if (++newIndex > 2) {
        newIndex = 0;
      }

      // change status
      this.tasks[index].status = this.availableStatus[newIndex];
    },

    firstCharUpper(str) {
      // finished
      // str.charAt(0).toUpperCase() - f -> F
      // str.slice(1) - will take all -> inished
      return str.charAt(0).toUpperCase() + str.slice(1);
    },
  },
};
</script>

<style scoped>
.pointer {
  cursor: pointer;
}

.finished {
  text-decoration: line-through;
}
</style>
