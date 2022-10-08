<template>
  <div class="hello">
    <el-button type="primary" plain v-on:click="buttonHandler">
      数据更新
    </el-button>
    <el-table :data="funcData" style="width: 100%">
      <el-table-column prop="fS_name" label="姓名"> </el-table-column>
      <el-table-column prop="syl_1y" label="近一个月收益率"> </el-table-column>
      <el-table-column prop="syl_3y" label="近三个月收益率"> </el-table-column>
      <el-table-column prop="syl_6y" label="近六个月收益率"> </el-table-column>
      <el-table-column prop="syl_1n" label="近一年收益率"> </el-table-column>
      <el-table-column label="现任基金经理">
        <template slot-scope="scope">
          <p>{{ scope.row.Data_currentFundManager?.name }}</p>
        </template>
      </el-table-column>
      <el-table-column label="经理在管规模">
        <template slot-scope="scope">
          <p>{{ scope.row.Data_currentFundManager?.fundSize }}</p>
        </template>
      </el-table-column>
    </el-table>
  </div>
</template>

<script>
import axios from "axios";
import { productIds } from "../assets/data";
const importJson = require("../assets/fundData.json");

export default {
  name: "HelloWorld",
  data() {
    return {
      funcData: importJson,
      canBeSave: false,
    };
  },
  watch: {
    canBeSave(flag) {
      if (flag) {
        this.canBeSave = false;
        var FileSaver = require("file-saver");
        var blob = new Blob([JSON.stringify(this.funcData)], { type: "" });
        FileSaver.saveAs(blob, "fundData.json");
      }
    },
  },
  methods: {
    buttonHandler() {
      this.funcData = []; // 置空，防止重复
      let singleData = {};
      for (let item of productIds) {
        setTimeout(() => {
          axios({
            method: "get",
            url: `/api/pingzhongdata/${item}.js?v=20220912172728`,
          })
            .then(function (response) {
              let data = response.data;
              let i = 0;
              let left = 0;
              let res = "";
              while (i < data.length - 1) {
                if (data[i] + data[i + 1] === "/*") {
                  res += data.slice(left + 1, i);
                  let j = i + 2;
                  while (j < data.length - 1) {
                    if (data[j] + data[j + 1] === "*/") {
                      i = j + 1;
                      left = j + 1;
                      break;
                    }
                    j++;
                  }
                }
                i++;
              }
              window.eval(res);
              /* eslint-disable */
              singleData = {
                fS_name,
                syl_1y,
                syl_3y,
                syl_6y,
                syl_1n,
                Data_currentFundManager: Data_currentFundManager[0],
              };
            })
            .finally(() => {
              this.funcData.push(singleData);
              console.log(this.funcData.length, productIds.length);
              if (this.funcData.length === productIds.length) {
                this.canBeSave = true;
              }
            });
        }, 500);
      }
    },
  },
};
</script>

<style scoped>
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
