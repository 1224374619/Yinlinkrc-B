<template>
  <div style="margin: 96px 0 0 0">
    <div class="resume-navs">
      <div>企业人才库</div>
    </div>
    <el-dialog
      title
      width="30%"
      :visible.sync="dialogetx"
      style="border-radius: 5px"
    >
      <div>
        <pdf ref="pdf" :src="url"></pdf>
      </div>
    </el-dialog>
    <el-dialog
      title
      :show-close="false"
      :append-to-body="true"
      :visible.sync="dialogVisible"
      width="25%"
      :before-close="handleClose"
    >
      <div>
        <div class="loading">
          <i
            style="font-size: 60px; color: #02b9b8"
            class="el-icon-loading"
          ></i>
        </div>
        <div class="loading-text">下载中...</div>
      </div>
    </el-dialog>
    <div class="resume-seconds">
      <el-form
        :inline="true"
        :model="formInline"
        label-width="100px"
        class="demo-form-inline"
      >
        <el-form-item label="关键词">
          <el-input
            style="width: 200px"
            placeholder="请输入关键字"
            suffix-icon="el-icon-search"
            v-model="formInline.keyword"
          ></el-input>
        </el-form-item>
        <br />
        <el-form-item label="性别">
          <el-select
            style="width: 200px"
            v-model="formInline.sex"
            placeholder="性别"
          >
            <el-option label="男" value="0"></el-option>
            <el-option label="女" value="1"></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="学历">
          <el-select
            style="width: 200px"
            v-model="formInline.degree"
            placeholder="学历"
          >
            <el-option
              v-for="item in optionsDegree"
              :key="item.value"
              :label="item.label"
              :value="item.value"
            ></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="现居地">
          <el-cascader
            style="width: 200px"
            v-model="formInline.city"
            :props="props"
            :options="city"
          ></el-cascader>
        </el-form-item>
        <el-form-item label="职位名称">
          <el-input
            style="width: 200px"
            placeholder="请输入职位名称"
            suffix-icon="el-icon-search"
            v-model="formInline.positionName"
          ></el-input>
        </el-form-item>
        <el-form-item label="海外工作经历">
          <el-select
            style="width: 200px"
            v-model="formInline.overseaYear"
            placeholder="海外工作经历"
          >
            <el-option label="无工作经验" value="0"></el-option>
            <el-option label="1年" value="1"></el-option>
            <el-option label="2年" value="2"></el-option>
            <el-option label="3年" value="3"></el-option>
            <el-option label="4年" value="4"></el-option>
            <el-option label="5年" value="5"></el-option>
            <el-option label="6年" value="6"></el-option>
            <el-option label="7年" value="7"></el-option>
            <el-option label="8年" value="8"></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="工作年限">
          <el-select
            style="width: 200px"
            v-model="formInline.workYear"
            placeholder="工作年限"
          >
            <el-option
              v-for="item in optionsWorkYear"
              :key="item.value"
              :label="item.label"
              :value="item.value"
            ></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="年龄">
          <el-date-picker
            style="width: 95px"
            v-model="formInline.birthdayMin"
            type="year"
            placeholder="选择"
          ></el-date-picker
          >-
          <el-date-picker
            style="width: 95px"
            v-model="formInline.birthdayMax"
            type="year"
            placeholder="选择"
          ></el-date-picker>
        </el-form-item>
        <el-form-item style="display: flex; justify-content: flex-end">
          <el-button @click="back">重置</el-button>
          <el-button
            @click="onSubmit"
            style="background: #ff7152; color: #ffffff"
            >查询</el-button
          >
        </el-form-item>
      </el-form>
      <div class="tab-operations">
        <span @click="uploadFile" style="color: #ff7152; font-size: 12px"
          >批量下载简历</span
        >
      </div>
      <el-table
        :data="tableData"
        style="width: 100%; margin: 50px 0 0 0"
        @selection-change="handleSelectionChange"
      >
        <el-table-column type="selection"></el-table-column>
        <el-table-column prop="fullName" label="姓名"></el-table-column>
        <el-table-column prop="sex" label="性别"></el-table-column>
        <el-table-column label="年龄">
          <template slot-scope="scope">
            <span>{{ scope.row.age }}岁</span>
          </template>
        </el-table-column>
        <el-table-column label="工作年限" show-overflow-tooltip>
          <template slot-scope="scope">
            <span>{{ scope.row.workAge }}年</span>
          </template>
        </el-table-column>
        <el-table-column label="海外工作年限" show-overflow-tooltip width="90">
          <template slot-scope="scope">
            <span>{{ scope.row.overseasAge }}年</span>
          </template>
        </el-table-column>
        <el-table-column
          prop="district"
          label="现居地"
          show-overflow-tooltip
        ></el-table-column>
        <el-table-column
          prop="degree"
          label="学历"
          show-overflow-tooltip
        ></el-table-column>
        <el-table-column
          prop="positionName"
          label="投递职位"
          show-overflow-tooltip
        ></el-table-column>
        <el-table-column
          prop="targetPosition"
          label="简历状态"
          show-overflow-tooltip
        >
          <template slot-scope="scope">
            <span>{{ scope.row.processedState | processedState }}</span>
          </template>
        </el-table-column>
        <el-table-column label="投递时间" width="95" show-overflow-tooltip>
          <template slot-scope="scope">
            <span>{{ scope.row.submittedTime | formatDateOne }}</span>
          </template>
        </el-table-column>
        <el-table-column prop="name" label="操作" width="90">
          <template slot-scope="scope">
            <el-button
              style="color: #ff7152"
              v-if="scope.row.isResumeAttached"
              @click="fileUrl(scope.row)"
              type="text"
              size="small"
              >查看附件</el-button
            >
            <el-button
              style="color: #ff7152"
              v-else
              @click="examing(scope.row)"
              type="text"
              size="small"
              >查看在线</el-button
            >
            <el-button
              @click="upload(scope.row)"
              style="color: #ff7152"
              type="text"
              size="small"
              >下载</el-button
            >
          </template>
        </el-table-column>
      </el-table>
      <el-pagination
        @size-change="handleSizeChange"
        @current-change="handleCurrentChange"
        :current-page="page.current"
        class="pagination"
        :page-sizes="page.pageSizeOpts"
        :page-size="page.pageSize"
        layout="total, sizes, prev, pager, next, jumper"
        :total="page.total"
      ></el-pagination>
    </div>
  </div>
</template>

<script>
import Cookies from "js-cookie";
import citys from "../../assets/city.json";
const timeUtil = require("../../timeUtil.js");
import qs from "qs";
import pdf from "vue-pdf";
export default {
  name: "home",
  components: { pdf },
  data() {
    return {
      url: "",
      dialogetx: false,
      city: [],
      props: {
        value: "code",
        label: "tag",
        children: "children",
      },
      dialogVisible: false,
      optionsDegree: [
        {
          value: "0",
          label: "初中及以下",
        },
        {
          value: "1",
          label: "中专/中技",
        },
        {
          value: "2",
          label: "高中",
        },
        {
          value: "3",
          label: "大专",
        },
        {
          value: "4",
          label: "本科",
        },
        {
          value: "5",
          label: "硕士",
        },
        {
          value: "6",
          label: "博士",
        },
      ],
      optionsWorkYear: [
        {
          value: "0",
          label: "无",
        },
        {
          value: "1",
          label: "1-3年",
        },
        {
          value: "2",
          label: "3-5年",
        },
        {
          value: "3",
          label: "5-10年",
        },
        {
          value: "4",
          label: "10年以上",
        },
      ],
      birthdayMin: "",
      birthdayMax: "",
      page: {
        total: 0,
        pageSize: 10,
        current: 1,
        pageSizeOpts: [10, 20, 30],
      },
      formInline: {
        keyword: "",
        sex: "",
        degree: "",
        city: "",
        positionName: "",
        overseaYear: "",
        workYear: "",
        birthdayMin: "",
        birthdayMax: "",
      },
      currentPage4: 4,
      tableData: [],
      arrResume: [],
      multipleSelection: [],
      arrPosition: [],
    };
  },
  methods: {
    //查看附件
    fileUrl(res) {
      this.$http
        .get(`/business-core/resumes/${res.id}/file/url`)
        .then((res) => {
          if (res.data.code === "200") {
            this.previewResume(res);
          } else {
          }
        })
        .catch((error) => {});
    },
    //doc docx预览
    previewResume(res) {
      let format = res.data.data.ext;
      if (format === "doc" || format === "docx") {
        let label = "resume-file";
        let params = res.data.data;
        var arr = JSON.stringify(params);
        let Logistics = this.$router.resolve(
          "/preview?obj=" + encodeURIComponent(arr)
        );
        window.open(Logistics.href, "_blank");
      } else {
        this.dialogetx = true;
        this.url = res.data.data.accessUrl;
      }
    },
    //下载简历
    handleSelectionChange(val) {
      this.arrResume = [];
      this.arrPosition = [];
      this.multipleSelection = val;
      this.multipleSelection.forEach((item, index, array) => {
        this.arrResume.push(item.id);
        this.arrPosition.push(item.positionId);
      });
    },
    //下载
    upload(tab) {
      // console.log(tab);
      // this.arrResume.push(tab.id);
      this.uploadFiles(tab);
    },
    uploadFiles(tab) {
      // let params = {
      //   resumeIds: this.arrResume,
      //   positionIds: this.arrPosition
      // };
      // let resumeList = qs.stringify(
      //   { resumeIds: this.arrResume },
      //   { arrayFormat: "repeat" }
      // );
      this.dialogVisible = true;
      this.$local
        .get(`/business-core/resumes/download/${tab.id}`, {
          responseType: "blob",
        })
        .then((res) => {
          this.dialogVisible = false;
          const disposition = res.headers["content-disposition"];
          let fileName = disposition.substring(
            disposition.indexOf("filename=") + 9,
            disposition.length
          );
          // iso8859-1的字符转换成中文
          fileName = decodeURI(escape(fileName));
          // 去掉双引号
          fileName = fileName.replace(/\"/g, "");
          const content = res.data;
          let blob = new Blob([res.data], {
            type: "application/vnd.ms-excel",
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
        .catch((error) => {
          this.dialogVisible = false;
          console.log(error)
          if (error.response.status === 404) {
            this.$notify.info({
              title: "消息",
              message: "页面丢失，请重新加载",
            });
          } else if (error.response.status === 403) {
            this.$notify.info({
              title: "消息",
              message: "登陆超时，请重新登录",
            });
          }  else {
            this.$notify.info({
              title: "消息",
              message: error.response.data.message,
            });
          }
        });
    },
    uploadFile() {
      let params = {
        resumeIds: this.arrResume,
        positionIds: this.arrPosition,
      };
      // let resumeList = qs.stringify(
      //   { resumeIds: this.arrResume },
      //   { arrayFormat: "repeat" }
      // );
      this.dialogVisible = true;
      this.$local
        .post("/business-core/resumes/batchPackageDownloadLong", params, {
          responseType: "blob",
        })
        .then((res) => {
          this.dialogVisible = false;
          const disposition = res.headers["content-disposition"];
          let fileName = disposition.substring(
            disposition.indexOf("filename=") + 9,
            disposition.length
          );
          // iso8859-1的字符转换成中文
          fileName = decodeURI(escape(fileName));
          // 去掉双引号
          fileName = fileName.replace(/\"/g, "");
          const content = res.data;
          let blob = new Blob([res.data], {
            type: "application/vnd.ms-excel",
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
        .catch((error) => {
          console.log(error.response)
          this.dialogVisible = false;
          if (error.response.status === 404) {
            this.$notify.info({
              title: "消息",
              message: "页面丢失，请重新加载",
            });
          } else if (error.response.status === 403) {
            this.$notify.info({
              title: "消息",
              message: "登陆超时，请重新登录",
            });
          }else if (error.response.status === 400) {
            this.$notify.info({
              title: "消息",
              message: "请选择要下载的文件",
            });
          } else {
            this.$notify.info({
              title: "消息",
              message: error.response.data.message,
            });
          }
        });
    },
    //列表
    platforms() {
      if (this.formInline.birthdayMin) {
        this.birthdayMin = this.formInline.birthdayMin.getTime();
        this.birthdayMax = this.formInline.birthdayMax.getTime();
      } else {
        this.birthdayMin = null;
        this.birthdayMax = null;
      }
      let workAgeMin;
      let workAgeMax;
      switch (this.formInline.workYear) {
        case "0":
          workAgeMin = 0;
          workAgeMax = 0;
          break;
        case "1":
          workAgeMin = 1;
          workAgeMax = 3;
          break;
        case "2":
          workAgeMin = 3;
          workAgeMax = 5;
          break;
        case "3":
          workAgeMin = 5;
          workAgeMax = 10;
          break;
        case "4":
          workAgeMin = 10;
          workAgeMax = null;
          break;
        default:
          workAgeMin = null;
          workAgeMax = null;
          break;
      }
      let city = timeUtil.CodeToTag(
        [
          this.formInline.city[0],
          this.formInline.city[1],
          this.formInline.city[2],
        ],
        this.city
      );
      let degree = timeUtil.qualifications(this.formInline.degree);
      let params = {
        birthdayMax: this.birthdayMax,
        birthdayMin: this.birthdayMin,
        city: city[1] ? city[1] : null,
        companyName: null,
        degree: degree ? degree : null,
        keywords: this.formInline.keyword ? this.formInline.keyword : null,
        overseasAge: Number(this.formInline.overseaYear)
          ? Number(this.formInline.overseaYear)
          : null,
        pageNum: 1,
        pageSize: 10,
        positionName: this.formInline.positionName
          ? this.formInline.positionName
          : null,
        positionCatalog: null,
        province: city[0] ? city[0] : null,
        sexCode: this.formInline.sex ? this.formInline.sex : null,
        sortBy: null,
        sortOrder: null,
        workAgeMax: workAgeMax ? workAgeMax : null,
        workAgeMin: workAgeMin ? workAgeMin : null,
      };
      this.$http
        .post("/business-core/CorporateTalentPool/databases/search", params)
        .then((res) => {
          let response = res.data.data.list;
          if (res.data.code == "200") {
            this.tableData = response;
            this.page.total = res.data.data.total;
          } else {
          }
        })
        .catch((error) => {});
    },
    //列表
    platform() {
      if (this.formInline.birthdayMin) {
        this.birthdayMin = this.formInline.birthdayMin.getTime();
        this.birthdayMax = this.formInline.birthdayMax.getTime();
      } else {
        this.birthdayMin = null;
        this.birthdayMax = null;
      }
      let workAgeMin;
      let workAgeMax;
      switch (this.formInline.workYear) {
        case "0":
          workAgeMin = 0;
          workAgeMax = 0;
          break;
        case "1":
          workAgeMin = 1;
          workAgeMax = 3;
          break;
        case "2":
          workAgeMin = 3;
          workAgeMax = 5;
          break;
        case "3":
          workAgeMin = 5;
          workAgeMax = 10;
          break;
        case "4":
          workAgeMin = 10;
          workAgeMax = null;
          break;
        default:
          workAgeMin = null;
          workAgeMax = null;
          break;
      }
      let city = timeUtil.CodeToTag(
        [
          this.formInline.city[0],
          this.formInline.city[1],
          this.formInline.city[2],
        ],
        this.city
      );
      let degree = timeUtil.qualifications(this.formInline.degree);
      let params = {
        birthdayMax: this.birthdayMax,
        birthdayMin: this.birthdayMin,
        city: city[1] ? city[1] : null,
        companyName: null,
        degree: degree ? degree : null,
        keywords: this.formInline.keyword ? this.formInline.keyword : null,
        overseasAge: Number(this.formInline.overseaYear)
          ? Number(this.formInline.overseaYear)
          : null,
        pageNum: this.page.current,
        pageSize: this.page.pageSize,
        positionName: this.formInline.positionName
          ? this.formInline.positionName
          : null,
        positionCatalog: null,
        province: city[0] ? city[0] : null,
        sexCode: this.formInline.sex ? this.formInline.sex : null,
        sortBy: null,
        sortOrder: null,
        workAgeMax: workAgeMax ? workAgeMax : null,
        workAgeMin: workAgeMin ? workAgeMin : null,
      };
      this.$http
        .post("/business-core/CorporateTalentPool/databases/search", params)
        .then((res) => {
          let response = res.data.data.list;
          if (res.data.code == "200") {
            this.tableData = response;
            this.page.total = res.data.data.total;
          } else {
          }
        })
        .catch((error) => {});
    },
    //查看
    examing(tab) {
      console.log(tab);
      this.$http
        .put(
          `/business-core/position/${tab.positionId}/resumes/${tab.id}/processing`
        )
        .then((res) => {
          let response = res.data.data.list;
          if (res.data.code == "200") {
          } else {
          }
        })
        .catch((error) => {});
      this.$router.push({
        path: "/resume/talent/Detail",
        query: {
          resumeIdes: tab.id,
          state: 2,
        },
      });
    },
    //重置
    back() {
      this.formInline = {
        keyword: "",
        sex: "",
        degree: "",
        city: "",
        positionName: "",
        overseaYear: "",
        workYear: "",
        birthdayMin: "",
        birthdayMax: "",
      };
    },
    onSubmit() {
      this.platforms();
    },
    handleSizeChange(val) {
      this.page.pageSize = val;
      this.page.current = 1;
      this.platform();
    },
    handleCurrentChange(val) {
      this.page.current = val;
      this.platform();
    },
  },
  created() {
    let token = Cookies.get("Btoken");
    this.city = citys.data;
    if (token) {
      this.platform();
    } else {
      this.$notify.info({
        title: "消息",
        message: "登陆超时，请重新登录",
      });
      this.$router.push({ path: "/login" });
    }
  },
  filters: {
    processedState(processedState) {
      var a;
      switch (processedState) {
        case "TO_PROCESS":
          a = "待处理";
          break;
        case "PROCESSING":
          a = "处理中";
          break;
        case "OFFERED":
          a = "录用";
          break;
        case "INTERVIEW":
          a = "面试";
          break;
        case "EMPLOYED":
          a = "已入职";
          break;
        case "UNFIT":
          a = "不合适";
          break;
      }
      return a;
    },
  },
};
</script>


<style lang="stylus" scoped>
.resume-navs {
  width: 100%;
  height: 50px;
  background: #FAFAFA;
  display: flex;
  flex-direction: row;
  justify-content: space-between;
  line-height: 50px;
  margin: -80px 0 0 0;

  div {
    margin: 0 0 0 20px;
    color: #02B9B8;
  }
}

.tab-operations {
  margin: -10px 0 10px 900px;
  font-family: PingFangSC-Regular;
}

.resume-seconds {
  width: 100%;
  height: auto;
  background: #ffffff;
  border: 1px solid rgba(244, 244, 244, 1);
  box-shadow: 0px 2px 10px 0px rgba(245, 245, 245, 1);
}

.demo-form-inline {
  margin: 20px 35px 0 20px;
}

.loading {
  text-align: center;
  margin: -20px 0 0 0;
}

.loading-text {
  font-size: 24px;
  color: #222222;
  text-align: center;
  margin: 30px 0 30px 0;
}
</style>

