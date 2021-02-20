<template>
  <div>
    <el-tabs
      v-model="tabTime"
      type="card"
      @tab-click="handleClick"
      class="timeTab"
    >
      <el-tab-pane
        :label="item.label"
        :name="item.name"
        v-for="(item, index) in tabList"
        :key="index"
      ></el-tab-pane>
    </el-tabs>
    <div class="table1">
      <div v-for="(item, index) in todos" :key="index" class="table-main">
        <!-- :class="{'table-scale':islarge===index}" -->
        <el-table
          border
          :data="item.tableData"
          style="flex: 1; flex-wrap: wrap"
          size="mini"
          :fit="false"
          @row-dblclick="scale($event, item)"
        >
          <el-table-column :label="item.title" align="center">
            <el-table-column
              prop="time"
              label="时间"
              width="63%"
              align="center"
            >
              <template slot-scope="scope">
                {{ scope.row.time | lookupFormatter(areaLookUp) }}
              </template>
            </el-table-column>
            <el-table-column
              :prop="time"
              :label="time"
              width="63%"
              v-for="(time, tindex) in timeColumn"
              :key="tindex"
              align="center"
            >
              <template slot-scope="scope">
                <span
                  v-if="
                    parseFloat(scope.row[time]) -
                      parseFloat(scope.row[time + 'a']) >
                    2
                  "
                  class="high"
                  >{{ scope.row[time] }}</span
                >
                <span
                  v-else-if="
                    parseFloat(scope.row[time]) -
                      parseFloat(scope.row[time + 'a']) <
                    -2
                  "
                  class="low"
                  >{{ scope.row[time] }}</span
                >
                <span v-else>{{ scope.row[time] }}</span>
              </template>
            </el-table-column>
            <el-table-column prop="area" width="63%" align="center">
              <template slot="header">
                <span>误差</span>
                <br />
                <span>范围</span>
              </template>
            </el-table-column>
            <el-table-column prop="midium" width="63%" align="center">
              <template slot="header">
                <span>误差</span>
                <br />
                <span>中数</span>
              </template>
            </el-table-column>
          </el-table-column>
        </el-table>
      </div>
    </div>
    <el-dialog
      :title="largeData.title"
      :visible.sync="islarge"
      width="100%"
      fullscreen
      :before-close="handleClose"
    >
      <el-table
        border
        :data="largeData.tableData"
        header-row-class-name="big-header"
      >
        <el-table-column prop="time" label="时间" align="center">
          <template slot-scope="scope">
            {{ scope.row.time | lookupFormatter(areaLookUp) }}
          </template></el-table-column
        >
        <el-table-column
          :prop="time"
          :label="time"
          v-for="(time, tindex) in timeColumn"
          :key="tindex"
          align="center"
        >
          <template slot-scope="scope">
            <span
              v-if="
                parseFloat(scope.row[time]) -
                  parseFloat(scope.row[time + 'a']) >
                2
              "
              class="high"
              >{{ scope.row[time] }}</span
            >
            <span
              v-else-if="
                parseFloat(scope.row[time]) -
                  parseFloat(scope.row[time + 'a']) <
                -2
              "
              class="low"
              >{{ scope.row[time] }}</span
            >
            <span v-else>{{ scope.row[time] }}</span>
          </template>
        </el-table-column>
        <el-table-column prop="area" align="center">
          <template slot="header">
            <span>误差</span>
            <br />
            <span>范围</span>
          </template>
        </el-table-column>
        <el-table-column prop="midium" align="center">
          <template slot="header">
            <span>误差</span>
            <br />
            <span>中数</span>
          </template>
        </el-table-column>
      </el-table>
    </el-dialog>
  </div>
</template>

<script>
import { getDate, getTableData } from '@/api/map'
export default {
  data() {
    return {
      timeColumn: [
        '-',
        '-',
        '-',
        '-',
        '-',
        '-',
        '-',
        '-',
        '-',
        '-',
      ],
      areaLookUp: [{
        value: '瓮安',
        key: '57728'
      }, {
        value: '福泉',
        key: '57821'
      }, {
        value: '贵定',
        key: '57824'
      }, {
        value: '龙里',
        key: '57913'
      }, {
        value: '惠水',
        key: '57912'
      }, {
        value: '长顺',
        key: '57818'
      }, {
        value: '罗甸',
        key: '57916'
      }, {
        value: '平塘',
        key: '57921'
      }, {
        value: '独山',
        key: '57922'
      }, {
        value: '荔波',
        key: '57926'
      }, {
        value: '三都',
        key: '57923'
      }, {
        value: '都匀',
        key: '57827'
      }],
      timestrampColumn: [],
      tabTime: '',
      todos: [
        {
          title: "滑动平均",
          tableData: [

          ]
        },
        {
          title: "最佳系数",
          tableData: [

          ]
        },
        {
          title: "双权重",
          tableData: [

          ]
        },
        {
          title: "多项式拟合",
          tableData: [

          ]
        }
      ],
      tabList: [],
      islarge: false,
      largeData: {}
    };
  },
  methods: {
    handleClick() {

    },
    fetchData() {
      getDate({ count: 10 }).then(res => {
        this.timeColumn = []
        for (let index = 0; index < 12; index++) {
          this.tabList[index] = { label: this.$moment(res[0].dtime_ec + 1000 * 12 * 60 * 60 * (index + 2)).format('DD日HH时'), name: res[0].dtime_ec + 1000 * 12 * 60 * 60 * (index + 2) + '' }
        }
        this.tabTime = this.tabList[0].name
        res.map(item => {
          this.timestrampColumn.push(item.dtime_ec)
          this.timeColumn.push(this.$moment(item.dtime_ec).format('DD-HH'))
        })
        getTableData({ area: JSON.stringify(this.timestrampColumn), date: this.tabTime, way: 0 }).then(res => {
          this.todos[0].tableData = res
        })
      })

    },
    scale: function (e, item) {
      this.largeData = item;
      this.islarge = true
    },
    handleClose() {
      this.islarge = false
    }
  },
  created() {
    this.todos.map((item, index) => { });
    this.fetchData()
  }
};
</script>
<style lang="scss" scoped>
.timeTab {
  margin-top: 15px;
}
</style>
<style lang="scss">
.big-header {
  th {
    background: #f5f7fa;
  }
}
.timeTab {
  .el-tabs__header {
    margin: 0;
  }
  .el-tabs__item {
    height: 30px;
    line-height: 30px;
  }
}
.table1 {
  display: flex;
  justify-content: center;
  flex-wrap: wrap;
  padding-top: 20px;
}
.table-main {
  width: calc((100vw - 280px) / 2);
  margin-bottom: 20px;
  margin-right: 10px;
  .el-table--mini td,
  .el-table--mini th {
    padding: 2px 0;
  }
  .el-table .cell {
    line-height: 21px;
  }
}
.high {
  color: #f56c6c;
}
.low {
  color: #e6a23c;
}
</style>