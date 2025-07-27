<template>
  <div>
    <div class="block">
      <el-date-picker
          v-model="dateVal"
          type="daterange"
          align="right"
          unlink-panels
          range-separator="至"
          start-placeholder="开始日期"
          end-placeholder="结束日期"
          :picker-options="pickerOptions"/>
    </div>
    <div id="charts" class="charts"/>

    <div>
      <el-table :data="appList" class="table" stripe>
        <el-table-column prop="name" label="APP" width="480">
          <template slot-scope="scope">
            {{scope.row.name}}
            <div style="display: inline-block;color: #29a1ff">
              {{scope.row.shortCode}}
            </div>
          </template>
        </el-table-column>
        <el-table-column prop="downloadCount" label="下载量"/>
      </el-table>
    </div>
  </div>
</template>

<script>
  import { mutations } from "@/store/store";
  import * as echarts from "echarts";

  export default {
    name: "DownLoadCount",
    data() {
      return {
        contentLoading: true,
        downloadDate: [],
        downloadTimes: [],
        dateVal: "",
        appList: [],
        num_list:[],
        pickerOptions: {
          onPick:(data)=>{
            if (!data.maxDate || !data.minDate) {
              return;
            }

            this.$http.get("downloadTimes").then((res) => {
              // 转换日期范围，并过滤数据
              const filteredData = res.data.filter(item => {
                const itemDate = new Date(item.date);
                return itemDate >= data.minDate && itemDate <= data.maxDate;
              });

              this.downloadDate=[];
              this.downloadTimes=[];
              for (let i = 0; i < filteredData.length; i++) {
                this.downloadDate.push(filteredData[i].date);
                this.downloadTimes.push(filteredData[i].downloadCount);
              }

              this.initDiv();
              // this.initDiv();
            });


          },
          disabledDate(time) {
            return time.getTime() < new Date("2024-8-18");
          },
          shortcuts: [{
            text: "最近一周",
            onClick(picker) {
              const end = new Date();
              const start = new Date();
              start.setTime(start.getTime() - 3600 * 1000 * 24 * 7);
              picker.$emit("pick", [start, end]);
            },
          }, {
            text: "最近一个月",
            onClick(picker) {
              const end = new Date();
              const start = new Date();
              start.setTime(start.getTime() - 3600 * 1000 * 24 * 30);
              picker.$emit("pick", [start, end]);
            },
          }, {
            text: "最近三个月",
            onClick(picker) {
              const end = new Date();
              const start = new Date();
              start.setTime(start.getTime() - 3600 * 1000 * 24 * 90);
              picker.$emit("pick", [start, end]);
            },
          }],
        },
      };
    },
    mounted() {
      mutations.setBreadcrumbs([
        { name: "我的应用", path: "/" },
        { name: "下载统计" },
      ]);
      this.getAppList();
      this.getDownloadCount();
    },
    methods: {
      get_one_data(time) {
        console.log("点击日期:", time);
        this.$http.get(`apps/downloads/${time}`).then(res => {
          this.appList = res.data;
          this.appList.sort((a, b) => b.downloadCount - a.downloadCount);
        }).catch(err => {
          console.log("获取指定日期下载数据失败:", err);
          this.$message.error("获取数据失败，请重试");});
      },
      getAppList() {
        this.contentLoading = true;
        this.$http
          .get("apps")
          .then((res) => {
            // 按下载量降序排序
            this.appList = res.data.sort((a, b) => b.downloadCount - a.downloadCount);
            this.contentLoading = false;
          })
          .catch(() => {
            this.contentLoading = false;
          });
      },
      getDownloadCount() {
        this.$http.get("downloadTimes").then((res) => {
          for (let i = 0; i < res.data.length; i++) {
            this.downloadDate.push(res.data[i].date);
            this.downloadTimes.push(res.data[i].downloadCount);
          }
          this.initDiv();
        });
      },
      initDiv() {
        let obj = echarts.init(document.getElementById("charts"));
        let option = {
          title: {
            text: "总下载量统计",
            left: 50,
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
          dataZoom: [
            {
              show: true,
              showDetail: true,
              type: "slider",
              start: 50,
              end: 100,
              bottom: 20,
            },
          ],
        };
        obj.setOption(option);
        obj.on("click", (params)=> {
          this.get_one_data(params.name);
        });
      },
    },
  };
</script>

<style scoped>
.charts {
  display: inline-block;
  height: 400px;
  width: 1020px;
  background-color: #fdfffb;
  box-shadow: 1px 1px 8px 1px gainsboro;
  padding-top: 20px;
}
.calendar{
  display: inline-block;
  margin-top: 10px;
  width: 1020px;
  background-color: #fdfffb;
  box-shadow: 1px 1px 8px 1px gainsboro;

}
.table {
  display: inline-block;
  margin-top: 10px;
  width: 1020px;
  background-color: #fdfffb;
  box-shadow: 1px 1px 8px 1px gainsboro;
}
.vm-main .vm-main-page .vm-page {
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
.el-table .cell {
  text-align: center;
}
</style>
