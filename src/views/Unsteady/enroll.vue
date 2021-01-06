<template>
  <div class="unsteady">
    <div class="title">
      <div class="title-nav">{{this.activityName}}</div>
    </div>
    <div class="content">
      <el-dialog
        title
        :show-close="false"
        :append-to-body="true"
        :visible.sync="dialogVisible"
        width="25%"
      >
        <div>
          <div class="loading">
            <i style="font-size:60px;color:#02b9b8;" class="el-icon-loading"></i>
          </div>
          <div class="loading-text">下载中...</div>
        </div>
      </el-dialog>
      <el-form :inline="true" :model="formInline" class="demo-form-inline">
        <el-form-item label="报名状态">
          <el-select
            style="width:150px"
            v-model="formInline.region"
            placeholder="请选择"
            @change="region()"
          >
            <el-option label="全部" value="0"></el-option>
            <el-option label="已报名" value="REGISTERED"></el-option>
            <el-option label="已取消" value="CANCELED"></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="审核状态">
          <el-select
            style="width:150px"
            v-model="formInline.regions"
            placeholder="请选择"
            @change="regions()"
          >
            <el-option label="待审核" value="PROCESSING"></el-option>
            <el-option label="审核通过" value="PASSED"></el-option>
            <el-option label="审核未通过" value="NOT_PASSED"></el-option>
          </el-select>
        </el-form-item>
        <!-- <el-form-item label>
          <el-input placeholder="搜索活动名称" suffix-icon="el-icon-search" v-model="input1"></el-input>
        </el-form-item>-->
      </el-form>
      <!-- <el-form :inline="true" :model="formInline" class="demo-form-inline">
        
        <el-form-item label>
          <el-input placeholder="搜索活动名称" suffix-icon="el-icon-search" v-model="input1"></el-input>
        </el-form-item>
      </el-form>-->
      <div class="excel" @click="uploadFile()">导处Excel</div>
      <div>
        <el-table
          :data="tableData"
          style="width: 923px;margin:10px 0 0 20px"
          @selection-change="handleSelectionChange"
        >
          <el-table-column type="selection" width="55"></el-table-column>
          <el-table-column
            v-for="(item,index) in this.formAttributes"
            :key="index"
            :label="item.chineseName"
            v-if="item.englishName === 'surname'"
            prop="registrations.surname"
          ></el-table-column>
          <el-table-column
            v-for="(item,index) in this.formAttributes"
            :key="index"
            show-overflow-tooltip
            :label="item.chineseName"
            v-if="item.englishName === 'email'"
            prop="registrations.email"
          ></el-table-column>
          <el-table-column
            v-for="(item,index) in this.formAttributes"
            :key="index"
            show-overflow-tooltip
            :label="item.chineseName"
            v-if="item.englishName === 'phone'"
            prop="registrations.phone"
          ></el-table-column>
          <el-table-column
            v-for="(item,index) in this.formAttributes"
            :key="index"
            :label="item.chineseName"
            v-if="item.englishName === 'sex'"
            prop="registrations.sex"
          ></el-table-column>
          <el-table-column
            v-for="(item,index) in this.formAttributes"
            :key="index"
            :label="item.chineseName"
            v-if="item.englishName === 'age'"
            prop="registrations.age"
          ></el-table-column>
          <el-table-column
            v-for="(item,index) in this.formAttributes"
            :key="index"
            :label="item.chineseName"
            v-if="item.englishName === 'position'"
            prop="registrations.position"
          ></el-table-column>
          <el-table-column
            v-for="(item,index) in this.formAttributes"
            :key="index"
            :label="item.chineseName"
            v-if="item.englishName === 'record'"
            prop="registrations.record"
          ></el-table-column>
          <el-table-column
            v-for="(item,index) in this.formAttributes"
            :key="index"
            :label="item.chineseName"
            v-if="item.englishName === 'school'"
            prop="registrations.school"
          ></el-table-column>
          <el-table-column
            v-for="(item,index) in this.formAttributes"
            :key="index"
            :label="item.chineseName"
            v-if="item.englishName === 'major'"
            prop="registrations.major"
          ></el-table-column>
          <el-table-column prop="registrationReview" v-if="!verify" label="审核状态">
            <template slot-scope="scope">{{scope.row.registrationReview|levels}}</template>
          </el-table-column>
          <el-table-column label="报名状态">
            <template slot-scope="scope">{{scope.row.registrationStatus|level}}</template>
          </el-table-column>
        </el-table>
      </div>
      <div style="margin:30px 0 20px 0" v-if="this.formInline.regions === 'PROCESSING'">
        <button
          class="enroll-button"
          style="margin:0 0 0 60px"
          @click="examinenotPass"
          type="danger"
        >审核不通过</button>
        <button class="enroll-buttons" @click="examinePass" type="primary">审核通过</button>
      </div>
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
    </div>
  </div>
</template>
<script>
export default {
  data() {
    return {
      dialogVisible: false,
      id: "",
      formInline: {
        user: "",
        region: "0",
        regions: "PROCESSING"
      },
      verify: true,
      tableData: [],
      multipleSelection: [],
      arrActivity: [],
      formAttributes: [],
      activityName: "",
      page: {
        total: 0,
        pageSize: 10,
        current: 1,
        pageSizeOpts: [10, 20, 30]
      }
    };
  },
  methods: {
    //select 改变
    region() {
      this.enroll();
    },
    regions() {
      this.enroll();
    },
    //审核不通过
    examinenotPass() {
      let params = {
        activityId: this.id,
        registrationIds: this.arrActivity
      };
      this.$http
        .put(`/business-core/activity/registration/noPass`, params)
        .then(res => {
          if (res.data.code == 200) {
            this.$notify({
              title: "成功",
              message: res.data.message,
              type: "success"
            });
          } else {
          }
        })
        .catch(error => {});
    },
    //审核通过
    examinePass() {
      let params = {
        activityId: this.id,
        registrationIds: this.arrActivity
      };
      this.$http
        .put(`/business-core/activity/registration/pass`, params)
        .then(res => {
          if (res.data.code == 200) {
            this.$notify({
              title: "成功",
              message: res.data.message,
              type: "success"
            });
          } else {
          }
        })
        .catch(error => {});
    },
    //报名列表
    enroll() {
      let params = {
        activityId: this.id,
        pageNum: this.page.current,
        pageSize: this.page.pageSize,
        registrationStatus:
          this.formInline.region === "0" ? null : this.formInline.region,
        registrationReview:
          this.formInline.regions === "0" ? null : this.formInline.regions,
        sortBy: null,
        sortOrder: null
      };
      this.$http
        .post(`/business-core/activity/registration/list`, params)
        .then(res => {
          if (res.data.code == 200) {
            this.tableData = res.data.data.activityRegistrationRowVO.list;
            this.formAttributes = res.data.data.formAttributes;
            this.page.total = res.data.data.activityRegistrationRowVO.total;
            this.activityName = res.data.data.activityName;
            if (res.data.data.activityMode === 0) {
              this.verify = true;
            } else {
              this.verify = false;
            }
          } else {
          }
        })
        .catch(error => {});
    },
    handleSelectionChange(val) {
      this.arrActivity = [];
      this.multipleSelection = val;
      this.multipleSelection.forEach((item, index, array) => {
        this.arrActivity.push(item.id);
      });
    },
    //下载
    uploadFile() {
      let activityList = {
        activityId: this.id,
        registrationIds: this.arrActivity
      };
      this.dialogVisible = true;
      this.$localo
        .post("/business-core/activity/registration/export", activityList, {
          responseType: "blob"
        })
        .then(res => {
          console.log(res.headers);
          this.dialogVisible = false;
          const disposition = res.headers["content-disposition"];
          console.log(disposition);
          let fileName = disposition.substring(
            disposition.indexOf("filename=") + 9,
            disposition.length
          );
          console.log(fileName.split(";"));

          // iso8859-1的字符转换成中文
          console.log(escape(fileName));
          fileName = decodeURI(fileName.split(";")[0]);
          console.log(fileName);
          // 去掉双引号
          fileName = fileName.replace(/\"/g, "");
          const content = res.data;
          let blob = new Blob([res.data], {
            type: "application/vnd.ms-excel"
          });
          console.log(blob);
          if (window.navigator.msSaveOrOpenBlob) {
            // console.log(2)
            navigator.msSaveBlob(blob, fileName);
          } else {
            // console.log(3)
            var link = document.createElement("a");
            link.href = window.URL.createObjectURL(blob);
            link.download = fileName;
            link.click();
            //释放内存
            window.URL.revokeObjectURL(link.href);
          }
        })
        .catch(error => {
          this.dialogVisible = false;
        });
    },
    handleSizeChange(val) {
      this.page.pageSize = val;
      this.page.current = 1;
    },
    handleCurrentChange(val) {
      this.page.current = val;
    }
  },
  created() {
    this.id = this.$route.query.id;
    this.enroll();
  },
  filters: {
    level(level) {
      var a;
      switch (level) {
        case "DID_NOT_SIGN_UP":
          a = "未报名";
          break;
        case "REGISTERED":
          a = "已报名";
          break;
        case "CANCELED":
          a = "已取消";
          break;
      }
      return a;
    },
    levels(levels) {
      var a;
      switch (levels) {
        case "PROCESSING":
          a = "待审核";
          break;
        case "PASSED":
          a = "审核通过";
          break;
        case "NOT_PASSED":
          a = "审核未通过";
          break;
      }
      return a;
    }
  }
};
</script>
<style lang="stylus">
.unsteady {
  margin: 30px 0 0 0;

  .title {
    width: 963px;
    height: 50px;
    background: #FAFAFA;
    display: flex;
    flex-direction: row;
    justify-content: space-between;

    .title-nav {
      font-family: PingFangSC-Medium;
      color: #02b9b8;
      font-size: 16px;
      margin: 17px 0 0 20px;
    }

    .title-content {
      font-family: PingFangSC-Medium;
      color: #737373;
      font-size: 14px;
      margin: 18px 20px 0 0;
    }
  }

  .content {
    width: 963px;
    height: auto;
    background: #FFFFFF;
    border: 1px solid rgba(246, 246, 246, 1);
    box-shadow: 0px 2px 12px 0px rgba(0, 0, 0, 0.06);

    .excel {
      display: flex;
      justify-content: flex-end;
      margin: 0 20px 0 0;
      font-family: PingFangSC-Regular;
      color: #FF9B26;
      font-size: 12px;
      cursor: pointer;
    }

    .enroll-button {
      background: #FC5454;
      border: 1px solid #FC5454;
      width: 80px;
      height: 28px;
      font-family: PingFangSC-Regular;
      color: #FFFFFF;
      font-size: 12px;
      border-radius: 5px;
    }

    .enroll-buttons {
      background: #02b9b8;
      border: 1px solid #02b9b8;
      width: 80px;
      height: 28px;
      font-family: PingFangSC-Regular;
      color: #FFFFFF;
      font-size: 12px;
      border-radius: 5px;
      margin: 0 0 0 20px;
    }
  }
}
</style>