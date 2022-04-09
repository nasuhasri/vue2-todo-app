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
              @click="changeStatus(index, task.id)"
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
            <div @click="editTask(index, task.id)">
              <span class="fa fa-pen"></span>
            </div>
          </td>
          <td>
            <div @click="deleteTask(index, task.id)">
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
      id: null,

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

      // submit new task
      if (this.editedTask === null) {
        // submit data using dummy data and store in array
        this.tasks.push({
          id: Math.floor(Math.random() * 1001),
          name: this.newTask,
          status: "to-do",
          priority: this.selectedPriority,
        });

        // submit data to db using rest api
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
        // edit task
        try {
          axios.put(`http://todo-api.test/api/todo/${this.id}`, {
            name: this.newTask,
            priority: this.selectedPriority,
            status: this.dataApi[this.editedTask].status,
          });

          // this.editedTask return the index of the array that need to be edited
          this.dataApi[this.editedTask].name = this.newTask;
          this.dataApi[this.editedTask].priority = this.selectedPriority;

          // make editedTask empty back
          this.editedTask = null;
        } catch (error) {
          console.log(error);
        }
      }

      this.newTask = "";
      this.selectedPriority = "";
    },

    deleteTask(index, id) {
      // delete dummy data in array using array method
      // at position index, remove 1 item
      this.tasks.splice(index, 1);

      // delete data using rest api
      try {
        axios
          .delete(`http://todo-api.test/api/todo/${id}`)
          .then((response) => {
            console.log(response);
          })
          .catch((error) => {
            console.log(error);
          });

        // filter data so page will display updated data
        // send task in arrow function where task.id !== id that has been deleted
        this.dataApi = this.dataApi.filter((task) => task.id !== id);
      } catch (error) {
        console.log(error);
      }
    },

    editTask(index, id) {
      this.newTask = this.dataApi[index].name;
      this.selectedPriority = this.dataApi[index].priority;

      this.editedTask = index;
      this.id = id;
    },

    changeStatus(index, id) {
      // get index of current task status
      let newIndex = this.availableStatus.indexOf(this.dataApi[index].status);

      // ++newIndex - next value
      if (++newIndex > 2) {
        newIndex = 0;
      }

      try {
        // change status in db
        axios.put(`http://todo-api.test/api/todo/${id}`, {
          name: this.dataApi[index].name,
          status: this.availableStatus[newIndex],
          priority: this.dataApi[index].priority,
        });

        // change status for page view
        this.dataApi[index].status = this.availableStatus[newIndex];
      } catch (error) {
        console.log(error);
      }
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
