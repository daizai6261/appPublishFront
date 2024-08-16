<template>
  <div>
    <div id="charts" class="charts"/>
    <div>
      <el-table
          :data="appList"
          class="table"
          stripe>
        <el-table-column
            prop="name"
            label="APP"
            width="480">
          <template slot-scope="scope">
            {{scope.row.name}}
            <div style="display: inline-block;color: #29a1ff">{{scope.row.shortCode}}</div>
          </template>
        </el-table-column>
        <el-table-column
            prop="downloadCount"
            label="今日下载量"/>
      </el-table>
    </div>
  </div>
</template>

<script>
  import {mutations} from "@/store/store";
  import * as echarts from "echarts";

  export default {
    name: "DownLoadCount",
    data() {
      return {
        contentLoading: true,
        downloadDate:[],
        downloadTimes: [],
        appList: [],
      };
    },
    mounted() {
      mutations.setBreadcrumbs([
        {name: "我的应用", path: "/"},
        {name: "下载统计"},
      ]);
      this.getAppList();
      this.getDownloadCount();
    },
    methods: {
      getAppList() {
        this.contentLoading = true;
        this.$http.get("apps").then(res => {
          this.appList = res.data;
          this.contentLoading = false;
        }).catch(() => {
          this.contentLoading = false;
        });
      },
      getDownloadCount() {
        this.$http.get("downloadTimes").then(res => {
          for (let i = 0; i < res.data.length; i++) {
            this.downloadDate.push(res.data[i].date);
            this.downloadTimes.push(res.data[i].downloadCount);
          }
          this.initDiv();
        });
      },
      initDiv() {
        let obj=echarts.init(document.getElementById("charts"));
        let option = {
          title: {
            text: "总下载量统计",
            left : 50,
          },
          tooltip: {
            trigger: "axis",
          },
          xAxis: {
            type: "category",
            data: this.downloadDate,
          },
          yAxis: {
            type: "value",
          },
          series: [
            {
              data: this.downloadTimes,
              type: "line",
            },
          ],
        };
        obj.setOption(option);
      },
    },
  };
</script>

<style scoped>
.charts{
    display: inline-block;
    height: 400px;
    width: 1020px;
    background-color: #fdfffb;
    box-shadow: 1px 1px 8px 1px gainsboro;
    padding-top: 20px;
}
.table{
    display: inline-block;
    margin-top: 10px;
    width: 1020px;
    background-color: #fdfffb;
    box-shadow: 1px 1px 8px 1px gainsboro;
}
.vm-main .vm-main-page .vm-page{
    padding-top: 20px;
    padding-bottom: 20px;
    text-align: center;
}
</style>
<style lang="scss">
.el-table__body-wrapper::-webkit-scrollbar {
  width: 8px;
}
.el-table__body-wrapper::-webkit-scrollbar-thumb {
  border-radius: 4px;
  height: 50px;
}
.el-table .cell{
  text-align: center;
}
</style>
