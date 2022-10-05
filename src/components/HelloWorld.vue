<template>
  <div class="hello">
    <el-button v-on:click="buttonHandler"> click </el-button>
    {{ data }}
  </div>
</template>

<script>
import axios from "axios";
export default {
  name: "HelloWorld",
  props: {
    msg: String,
    data: {},
  },
  methods: {
    buttonHandler() {
      let productIds = ["012002"];
      for (let item of productIds) {
        axios({
          method: "get",
          url: `/api/pingzhongdata/${item}.js?v=20220912172728`,
        }).then(function (response) {
          let data = JSON.stringify(response.data);
          console.log(data);
          //  coding here
        });
      }
    },
  },
};
</script>

<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
