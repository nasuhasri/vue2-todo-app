<template>
  <div class="container">
    <h1>Hello World from TodoApp.vue</h1>
    <h2 class="text-center mt-5 mb-5">My Vue Todo App</h2>

    <!--- Input -->
    <div class="d-flex mb-5">
      <!-- v-model: 2-way binding -->
      <!-- in script tag, it is empty string but if we put value to it, it will appear in this input tag -->
      <input v-model="task" type="text" class="form-control" placeholder="Enter task">
      <button @click="submitTask" class="btn btn-warning rounded-0">SUBMIT</button>
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
        <tr v-for="(task, index) in tasks" :key="index">
          <td>{{ task.id }}</td>
          <td>{{ task.name }}</td>
          <td>{{ task.status }}</td>
          <td>{{ task.priority }}</td>
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
export default { 
  name: 'HelloWorld',
  
  data(){ 
    return { 
      // to get value from input
      // var for new task
      task: '',
      // var for edited task by user
      editedTask: null,
      tasks: [
        {
          id: 1, 
          name: "Steal bananas from the supermarket.", 
          status: "to-do",
          priority: "low" 
        }, 
        { 
          id: 2,
          name: "Eat 1 kg chocolate in 1 hour.", 
          status: "in-progress", 
          priority: "medium"
        }, 
        {
          id: 3,
          name: "Create YouTube video.", 
          status: "finished", 
          priority: "high"
        }, 
        {
          id: 4, 
          name: "Create YouTube video.", 
          status: "finished", 
          priority: "urgent" 
        },

      ] 
    } 
  },
  
  methods: {
    submitTask() {
      // using v-model, we can get the value of the input tag
      // console.log(this.task)

      if(this.task.length === 0) { return }

      if(this.editedTask === null) {
        this.tasks.push({
          id: Math.floor(Math.random() * 1001),
          name: this.task,
          status: 'to-do',
          priority: 'low'
        })
      } else {
        // this.editedTask return the index of the array that need to be edited
        this.tasks[this.editedTask].name = this.task;
        this.editedTask = null;
      }

      this.task = ''
    },

    deleteTask(index){
      // splice() - can add/remove items in array
      // at position index, remove 1 item
      this.tasks.splice(index, 1);
    },

    editTask(index){
      this.task = this.tasks[index].name;
      this.editedTask = index;
    }
  }
}

</script>

<style scoped>

</style>
