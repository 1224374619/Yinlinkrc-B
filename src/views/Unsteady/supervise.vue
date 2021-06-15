<template>
  <div class="unsteady">
    <div class="title"></div>
    <div class="content">
      <div class="tab-operations">
        <el-input placeholder="搜索活动名称" v-model="supervise"><i @click="activityList()" style="line-height:40px" slot="suffix" class="el-icon-search"></i></el-input>
      </div>
      <el-tabs v-model="activeName" type="card" @tab-click="handleClick" class="card">
        <el-tab-pane name="first">
          <span slot="label">全部</span>
          <el-table :data="tableData" style="width: 100%">
            <el-table-column prop="activityPosterUrl" label="活动封面">
              <template slot-scope="scope">
               <img :src="scope.row.activityPosterUrl" style="height:52px;width:91px" class="head_pic" />
              </template>
            </el-table-column>
            <el-table-column prop="activityName" label="活动名称"></el-table-column>
            <el-table-column prop="registrationNum" label="报名人数">
              <template slot-scope="scope">{{scope.row.registeredNum}}/{{scope.row.registrationNum}}</template>
            </el-table-column>
            <el-table-column label="活动开始时间" show-overflow-tooltip>
              <template slot-scope="scope">{{scope.row.activityStartTime|formatDateThree}}</template>
            </el-table-column>
            <el-table-column label="活动截止时间" show-overflow-tooltip>
              <template slot-scope="scope">{{scope.row.activityEndTime|formatDateThree}}</template>
            </el-table-column>
            <el-table-column label="发布时间" show-overflow-tooltip>
              <template slot-scope="scope">{{scope.row.createdTime|formatDateThree}}</template>
            </el-table-column>
            <el-table-column label="活动状态">
              <template slot-scope="scope">{{scope.row.activityState|level}}</template>
            </el-table-column>
            <el-table-column label="操作">
              <template slot-scope="scope">
                <el-button @click="handleClickreport(scope.row)" type="text" size="small">报名详情</el-button>
                <el-button  type="text" size="small" @click="share('https://www.yinlinkrc.com/#/appraiseDetail?id='+ scope.row.id)">分享</el-button>
                <el-button type="text" @click="handleClickdetail(scope.row)" size="small">查看</el-button>
              </template>
            </el-table-column>
          </el-table>
          <el-pagination
            @size-change="handleSizeChange"
            @current-change="handleCurrentChange"
            :current-page="page.current"
            :page-sizes="page.pageSizeOpts"
            class="pagination"
            :page-size="page.pageSize"
            layout="total, sizes, prev, pager, next, jumper"
            :total="page.total"
          ></el-pagination>
        </el-tab-pane>
        <el-tab-pane label="待审核" name="second">
          <el-table :data="tableData" style="width: 100%">
            <el-table-column prop="activityPosterUrl" label="活动封面">
              <template slot-scope="scope">
                <img :src="scope.row.activityPosterUrl" style="height:52px;width:91px" class="head_pic" />
              </template>
            </el-table-column>
            <el-table-column prop="activityName" label="活动名称"></el-table-column>
            <el-table-column prop="registrationNum" label="报名人数">
              <template slot-scope="scope">{{scope.row.registeredNum}}/{{scope.row.registrationNum}}</template>
            </el-table-column>
            <el-table-column label="活动开始时间" show-overflow-tooltip>
              <template slot-scope="scope">{{scope.row.activityStartTime|formatDateThree}}</template>
            </el-table-column>
            <el-table-column label="活动截止时间" show-overflow-tooltip>
              <template slot-scope="scope">{{scope.row.activityEndTime|formatDateThree}}</template>
            </el-table-column>
            <el-table-column label="发布时间" show-overflow-tooltip>
              <template slot-scope="scope">{{scope.row.createdTime|formatDateThree}}</template>
            </el-table-column>
            <el-table-column label="活动状态">
              <template slot-scope="scope">{{scope.row.activityState|level}}</template>
            </el-table-column>
            <el-table-column label="操作">
              <template slot-scope="scope">
                <el-button @click="handleClickreport(scope.row)" type="text" size="small">报名详情</el-button>
                <el-button  type="text" size="small" @click="share('https://www.yinlinkrc.com/#/appraiseDetail?id='+ scope.row.id)">分享</el-button>
                <el-button type="text" @click="handleClickdetail(scope.row)" size="small">查看</el-button>
              </template>
            </el-table-column>
          </el-table>
          <el-pagination
            @size-change="handleSizeChange"
            @current-change="handleCurrentChange"
            :current-page="page.current"
            :page-sizes="page.pageSizeOpts"
            class="pagination"
            :page-size="page.pageSize"
            layout="total, sizes, prev, pager, next, jumper"
            :total="page.total"
          ></el-pagination>
        </el-tab-pane>
        <el-tab-pane name="third">
          <template slot="label">
            <el-dropdown>
              <span class="el-dropdown-link">审核通过</span>
              <el-dropdown-menu slot="dropdown">
                <el-dropdown-item @click.native="activitytatus(null)">全部</el-dropdown-item>
                <el-dropdown-item @click.native="activitytatus('REGISTRATION_NOT_STARTED')">报名未开始</el-dropdown-item>
                <el-dropdown-item @click.native="activitytatus('REGISTRATION_IN_PROGRESS')">报名进行中</el-dropdown-item>
                <el-dropdown-item @click.native="activitytatus('REGISTRATION_IS_UP')">报名已截至</el-dropdown-item>
              </el-dropdown-menu>
            </el-dropdown>
          </template>
          <el-table :data="tableData" style="width: 100%">
            <el-table-column prop="activityPosterUrl" label="活动封面">
              <template slot-scope="scope">
                <img :src="scope.row.activityPosterUrl" style="height:52px;width:91px" class="head_pic" />
              </template>
            </el-table-column>
            <el-table-column prop="activityName" label="活动名称"></el-table-column>
            <el-table-column prop="registrationNum" label="报名人数">
              <template slot-scope="scope">{{scope.row.registeredNum}}/{{scope.row.registrationNum}}</template>
            </el-table-column>
            <el-table-column label="活动开始时间" show-overflow-tooltip>
              <template slot-scope="scope">{{scope.row.activityStartTime|formatDateThree}}</template>
            </el-table-column>
            <el-table-column label="活动截止时间" show-overflow-tooltip>
              <template slot-scope="scope">{{scope.row.activityEndTime|formatDateThree}}</template>
            </el-table-column>
            <el-table-column label="发布时间" show-overflow-tooltip>
              <template slot-scope="scope">{{scope.row.createdTime|formatDateThree}}</template>
            </el-table-column>
            <el-table-column label="活动状态">
              <template slot-scope="scope">{{scope.row.activityState|level}}</template>
            </el-table-column>
            <el-table-column label="操作">
              <template slot-scope="scope">
                <el-button @click="handleClickreport(scope.row)" type="text" size="small">报名详情</el-button>
                <el-button  type="text" size="small" @click="share('https://www.yinlinkrc.com/#/appraiseDetail?id='+ scope.row.id)">分享</el-button>
                <el-button type="text" @click="handleClickdetail(scope.row)" size="small">查看</el-button>
              </template>
            </el-table-column>
          </el-table>
          <el-pagination
            @size-change="handleSizeChange"
            @current-change="handleCurrentChange"
            :current-page="page.current"
            :page-sizes="page.pageSizeOpts"
            class="pagination"
            :page-size="page.pageSize"
            layout="total, sizes, prev, pager, next, jumper"
            :total="page.total"
          ></el-pagination>
        </el-tab-pane>
        <el-tab-pane label="审核未通过" name="fourth">
          <el-table :data="tableData" style="width: 100%">
            <el-table-column prop="activityPosterUrl" label="活动封面">
              <template slot-scope="scope">
               <img :src="scope.row.activityPosterUrl" style="height:52px;width:91px" class="head_pic" />
              </template>
            </el-table-column>
            <el-table-column prop="activityName" label="活动名称"></el-table-column>
            <el-table-column prop="registrationNum" label="报名人数">
              <template slot-scope="scope">{{scope.row.registeredNum}}/{{scope.row.registrationNum}}</template>
            </el-table-column>
            <el-table-column label="活动开始时间" show-overflow-tooltip>
              <template slot-scope="scope">{{scope.row.activityStartTime|formatDateThree}}</template>
            </el-table-column>
            <el-table-column label="活动截止时间" show-overflow-tooltip>
              <template slot-scope="scope">{{scope.row.activityEndTime|formatDateThree}}</template>
            </el-table-column>
            <el-table-column label="发布时间" show-overflow-tooltip>
              <template slot-scope="scope">{{scope.row.createdTime|formatDateThree}}</template>
            </el-table-column>
            <el-table-column label="活动状态">
              <template slot-scope="scope">{{scope.row.activityState|level}}</template>
            </el-table-column>
            <el-table-column label="操作">
              <template slot-scope="scope">
                <el-button @click="handleClickreport(scope.row)" type="text" size="small">报名详情</el-button>
                <el-button  type="text" size="small" @click="share('https://www.yinlinkrc.com/#/appraiseDetail?id='+ scope.row.id)">分享</el-button>
                <el-button type="text" @click="handleClickdetail(scope.row)" size="small">查看</el-button>
              </template>
            </el-table-column>
          </el-table>
          <el-pagination
            @size-change="handleSizeChange"
            @current-change="handleCurrentChange"
            :current-page="page.current"
            :page-sizes="page.pageSizeOpts"
            class="pagination"
            :page-size="page.pageSize"
            layout="total, sizes, prev, pager, next, jumper"
            :total="page.total"
          ></el-pagination>
        </el-tab-pane>
      </el-tabs>
    </div>
  </div>
</template>
<script>
export default {
  data() {
    return {
      activeName: "first",
      activityRegistrationState: null,
      activityState: null,
      supervise: "",
      tableData: [],
      page: {
        total: 0,
        pageSize: 10,
        current: 1,
        pageSizeOpts: [10, 20, 30]
      }
    };
  },
  methods: {
    //分享活动
    share(data) {
      console.log(data)
      let url = data;
      let oInput = document.createElement("input");
      oInput.value = url;
      document.body.appendChild(oInput);
      oInput.select(); // 选择对象;
      console.log(oInput.value);
      document.execCommand("Copy"); // 执行浏览器复制命令
      this.$notify({
        title: "成功",
        message: "复制带剪切板成功",
        type: "success"
      });
      oInput.remove();
    },
    //报名状态筛选
    activitytatus(res) {
      console.log(res);
      this.activeName = "third";
      this.activityState = "HAVE_PUBLISHED";
      this.activityRegistrationState = res;
      this.activityList();
    },
    //活动列表
    activityList() {
      let params = {
        activityName: this.supervise ? this.supervise : null,
        activityRegistrationState: this.activityRegistrationState,
        activityState: this.activityState,
        pageNum: this.page.current,
        pageSize: this.page.pageSize,
        sortBy: null,
        sortOrder: null
      };
      this.$http
        .post("/business-core/activity/list", params)
        .then(res => {
          if (res.data.code == 200) {
            this.tableData = res.data.data.list;
            this.page.total = res.data.data.total;
          } else {
          }
        })
        .catch(error => {});
    },
    handleClick(tab, event) {
      console.log(tab, event);
      if (tab.name === "first") {
        this.activityState = null;
        this.activityList();
      } else if (tab.name === "second") {
        this.activityState = "PROCESSING";
        this.activityList();
      } else if (tab.name === "third") {
        this.activityState = "HAVE_PUBLISHED";
        this.activityList();
      } else if (tab.name === "fourth") {
        this.activityState = "NOT_PASSED";
        this.activityList();
      }
    },
    //报名详情
    handleClickreport(row) {
      this.$router.push({
        path: "/unsteady/enroll",
        query: { id: row.id }
      });
    },
    //查看详情
    handleClickdetail(row) {
      console.log(row);
      this.$router.push({
        path: "/unsteady/promotionalDetail",
        query: { id: row.id }
      });
    },
    handleSizeChange(val) {
      this.page.pageSize = val;
      this.page.current = 1;
      this.activityList();
    },
    handleCurrentChange(val) {
      this.page.current = val;
      this.activityList();
    }
  },
  created() {
    this.activityList();
  },
  filters: {
    level(level) {
      var a;
      switch (level) {
        case "NOT_KNOWN":
          a = "未知";
          break;
        case "PROCESSING":
          a = "待审核";
          break;
        case "HAVE_PUBLISHED":
          a = "已发布";
          break;
        case "NOT_PASSED":
          a = "未通过";
          break;
        default:
          a = "已下线";
          break;
      }
      return a;
    }
  }
};
</script>

<style lang="stylus" scoped>
.unsteady {
  margin: 30px 0 0 0;

  .title {
    width: 963px;
    height: 50px;
    background: #FAFAFA;
  }

  .content {
    width: 963px;
    height: auto;
    background: #FFFFFF;
    border: 1px solid rgba(246, 246, 246, 1);
    box-shadow: 0px 2px 12px 0px rgba(0, 0, 0, 0.06);

    .card {
      width: 923px;
      margin: 30px 0 0 20px;
    }

    .tab-operations {
      position: absolute;
      margin: 25px 0 0 700px;
      z-index: 999;
    }
  }
}
</style>