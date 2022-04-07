<template>
  <div>
    {{ info }}
  </div>
</template>

<script>
const axios = require("axios");

export default {
  name: "TestAPI",

  data() {
    return {
      info: null,
    };
  },

  mounted() {
    axios
      .get("http://todo-api.test/api/todo")
      .then((response) => {
        this.info = response.data.todos;
      })
      .catch((error) => {
        console.log(error);
      });
  },
};
</script>
