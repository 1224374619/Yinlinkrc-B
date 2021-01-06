<template>
  <div style="margin:96px 0 0 0;">
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
    <el-dialog
      title
      :visible.sync="centerDialogVisible"
      width="20%"
      center
      :show-close="false"
      style="margin:100px 0 0 0"
    >
      <div
        style="text-align:center;font-family: PingFangSC-Medium;color: #111111;font-size:20px"
      >确定发送面试邀请?</div>
      <div style="margin:50px 0 0 18%">
        <el-button @click="centerDialogVisible = false">取 消</el-button>
        <el-button style="margin:0 0 0 40px" type="primary" @click="transmit()">确 定</el-button>
      </div>
      <div style="height:35px"></div>
    </el-dialog>
    <el-dialog title="发送面试邀请" :visible.sync="centerDialogVisibles" width="44%" center>
      <div style="margin:30px 0 0 0">
        <el-form
          :model="ruleForm"
          :rules="rulesQuiz"
          ref="ruleForm"
          :inline="true"
          label-width="100px"
          class="demo-ruleForm"
        >
          <el-form-item label="面试公司">
            <el-input
              style="width:580px"
              v-model="ruleForm.companyName"
              :disabled="true"
              placeholder="面试公司"
            ></el-input>
          </el-form-item>
          <el-form-item label="面试时间" prop="oralTime">
            <el-date-picker
              style="width:580px"
              v-model="ruleForm.oralTime"
              type="datetime"
              placeholder="选择日期时间"
            ></el-date-picker>
          </el-form-item>
          <el-form-item label="面试职位">
            <el-input
              style="width:234px"
              :disabled="true"
              v-model="ruleForm.positions"
              placeholder="面试职位"
            ></el-input>
          </el-form-item>
          <el-form-item label="薪资范围">
            <el-input
              style="width:234px"
              :disabled="true"
              v-model="ruleForm.dalary"
              placeholder="薪资范围"
            ></el-input>
          </el-form-item>
          <el-form-item label="面试地址" prop="address">
            <el-select style="width:580px" v-model="ruleForm.address" placeholder>
              <el-option
                v-for="item in addressList"
                :key="item.id"
                class="option"
                :label="item.detail"
                :value="item.id"
              >
                <span style="float: left">{{ item.detail }}</span>
              </el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="联系人" prop="user">
            <el-input style="width:234px" v-model="ruleForm.user" placeholder="联系人"></el-input>
          </el-form-item>
          <el-form-item label="联系电话" prop="phone">
            <el-input style="width:234px" v-model="ruleForm.phone" placeholder="联系电话"></el-input>
          </el-form-item>
          <el-form-item label="备注">
            <el-input type="textarea" style="width:580px" v-model="ruleForm.desc" placeholder></el-input>
          </el-form-item>
          <el-form-item style="margin:0 0 0 40%">
            <el-button @click="preview()" style="margin:0 50px 0 0" plain>预览</el-button>
            <el-button @click="submitForm('ruleForm')" type="primary">发送</el-button>
          </el-form-item>
        </el-form>
      </div>
    </el-dialog>
    <div class="personal-title">
      <el-dialog title="面试邀请预览" :visible.sync="dialogVisibledit" width="32%" center>
        <div style="margin:0 0 0 20px">
          <div class="titles">{{this.ruleForm.user}} 您好！</div>
          <div class="titles">恭喜您获得面试邀请，现邀请您参加面试，面试信息如下：</div>
          <div class="content" style="margin:20px 0 0 0">
            面试公司：
            <span>{{this.ruleForm.companyName}}</span>
          </div>
          <div class="content">
            面试时间：
            <span>{{this.oralTime|formatDateOne}}</span>
          </div>
          <div class="content">
            面试职位：
            <span>{{this.ruleForm.positions}} {{this.ruleForm.dalary}}</span>
          </div>
          <div class="content">
            面试地点：
            <span>{{this.companyAddress}}</span>
          </div>
          <div class="content">
            联系人：
            <span>{{this.ruleForm.user}}</span>
          </div>
          <div class="content">
            联系电话：
            <span>{{this.ruleForm.phone}}</span>
          </div>
          <div class="content">
            备注：
            <span>请携带相关证明</span>
          </div>
          <div class="aside">
            如有疑问或建议，可发送邮件至
            <el-tooltip placement="top" effect="light">
              <div slot="content">{{this.linktitle}}</div>
              <span
                class="tabs-read"
                :data-clipboard-text="this.textlink"
                @click="copy"
              >service@163.com</span>
            </el-tooltip>
          </div>
          <div class="foot">请您最晚于面试前12小时确认是否参加面试</div>
        </div>
        <div style="margin:30px 0 0 200px">
          <el-button type="primary" @click="dialogVisibledit = false">确定</el-button>
        </div>
      </el-dialog>
    </div>
    <div class="resume-navs">
      <div>简历详情</div>
    </div>
    <div class="resume-seconds">
      <div class="resume" v-if="this.resumeDeta.base !== underfined">
        <div class="block">
          <img
            width="100px;height:100px"
            style="border-radius:50px"
            :src="resumeDeta.base.avatarUrl"
          />
        </div>
        <div class="infor">
          <div style="font-size:20px;margin:0 0 0 10px;color:#373737">{{resumeDeta.base.fullName}}</div>
          <div style="margin:15px 0 0 0">
            <span>{{resumeDeta.base.city}}</span>
            <span>|</span>
            <span>{{resumeDeta.base.degree}}</span>
            <span>|</span>
            <span>{{resumeDeta.base.workAge}}年工作经验</span>
            <span>|</span>
            <span>{{resumeDeta.base.age}}岁</span>
            <span>|</span>
            <span>{{resumeDeta.base.politicalStatus}}</span>
          </div>
          <div style="margin:5px 0 0 0">
            <span>
              <i class="el-icon-mobile-phone" style="margin:0 5px 0 0"></i>
              {{resumeDeta.base.phone}}
            </span>
            <span>|</span>
            <span>
              <i class="el-icon-message" style="margin:0 5px 0 0"></i>
              {{resumeDeta.base.email}}
            </span>
          </div>
        </div>
      </div>
      <div class="resume" v-else>
        <div class="title" style="margin-bottom:10px">基本信息</div>
      </div>
      <div class="jotension">
        <div class="title">求职意向</div>
        <div v-if="this.resumeDeta.target !== underfined">
          <div class="jotension-content">
            <div>职位类型：{{resumeDeta.target.positionCatalogs[0].third}}</div>
            <div>工作城市：{{resumeDeta.target.cities[0].province}}</div>
          </div>
          <div class="jotension-content">
            <div>企业行业：{{resumeDeta.target.industries[0].secondary}}</div>
            <div style>薪资范围：{{resumeDeta.target.salaryMin}}-{{resumeDeta.target.salaryMax}}k</div>
          </div>
          <div class="jotension-content">
            <div>求职状态：{{resumeDeta.target.jobSearchStatus}}</div>
            <div style>工作类型：{{resumeDeta.target.jobType}}</div>
          </div>
        </div>
      </div>
      <div class="jotension">
        <div class="title">教育经历</div>
        <div v-for="(item,index) in resumeDeta.eduXps" :key="index">
          <div class="jotension-content">
            <div>{{item.school}}</div>
            <div>{{item.beginTime|formatDateOne}}-{{item.endTime|formatDateOne}}</div>
          </div>
          <div class="jotension-content">
            <div>{{item.major}} | {{item.degree}} | {{ item.isUnified ? '统招' : '非统招'}}</div>
          </div>
        </div>
      </div>
      <div class="jotension">
        <div class="title">工作经历</div>
        <div v-for="(item,index) in resumeDeta.workXps" :key="index">
          <div class="jotension-content">
            <div>{{item.company}}</div>
            <div>{{item.beginTime|formatDateOne}}-{{item.endTime|formatDateOne}}</div>
          </div>
          <div class="jotension-content">
            <div>{{item.position}} | {{item.salaryBeforeTax}}k</div>
          </div>
          <div class="jotension-content">
            <div>工作内容：{{item.description}}</div>
          </div>
        </div>
      </div>
      <div class="jotension">
        <div class="title">项目经历</div>
        <div v-for="(item,index) in resumeDeta.projects" :key="index">
          <div class="jotension-content">
            <div>{{item.project}} | {{item.company}}</div>
            <div>{{item.beginTime|formatDateOne}}-{{item.endTime|formatDateOne}}</div>
          </div>
          <div class="jotension-content">
            <div>个人职责：{{item.duty}}</div>
          </div>
          <div class="jotension-content">
            <div>项目介绍：{{item.description}}</div>
          </div>
        </div>
      </div>
      <div class="jotension">
        <div class="title">培训经历</div>
        <div class="train-content" v-for="(item,index) in resumeDeta.trainings" :key="index">
          <div>{{item.lesson}}</div>
          <div>{{item.institution}}</div>
          <div
            style="margin:0 0 0 300px"
          >{{item.beginTime|formatDateOne}}-{{item.endTime|formatDateOne}}</div>
        </div>
      </div>
      <div class="jotension">
        <div class="title">职称等级</div>
        <div class="train-content" v-for="(item,index) in resumeDeta.qualifications" :key="index">
          <div>{{item.qual}}</div>
        </div>
      </div>
      <div class="jotension">
        <div class="title">专业技能</div>
        <div class="train-content" v-for="(item,index) in resumeDeta.skills" :key="index">
          <div>{{item.skill}}</div>
          <div>{{item.level|level}}</div>
          <!-- <div>证书附件，点击查看大图</div> -->
          <!-- <div>{{item.beginTime|formatDateOne}}-{{item.endTime|formatDateOne}}</div> -->
        </div>
      </div>
      <div class="jotension">
        <div class="title">荣誉奖项</div>
        <div class="jotension-content" v-for="(item,index) in resumeDeta.awards" :key="index">
          <div>{{item.award}}</div>
          <!-- <div>{{item.}}</div>
          <div>证书附件，点击查看大图</div>-->
          <div>{{item.acquiredTime|formatDateOne}}</div>
        </div>
      </div>
      <div class="jotension">
        <div class="title">自我评价</div>
        <div class="train-content" v-if="this.resumeDeta.evaluation !== underfined">
          <div style="width:700px;">{{resumeDeta.evaluation.content}}</div>
        </div>
      </div>
      <div class="pagination" v-if="this.processedState === 'UNFIT'">
        <el-button plain @click="back">返回</el-button>
        <el-button type="text" @click="uploadFile">立即下载</el-button>
      </div>
      <div class="pagination" v-else-if="this.processedState === 'EMPLOYED'">
        <el-button plain @click="back">返回</el-button>
        <el-button type="text" @click="uploadFile">立即下载</el-button>
      </div>
      <div class="pagination" v-else-if="this.processedState === 'PROCESSING'">
        <el-button plain @click="back">返回</el-button>
        <el-button type="primary" @click="unfit">不合格</el-button>
        <el-button type="primary" @click="info">发送面试</el-button>
        <el-button type="text" @click="uploadFile">立即下载</el-button>
      </div>
      <div class="pagination" v-else-if="this.processedState === 'TO_PROCESS'">
        <el-button plain @click="back">返回</el-button>
        <el-button type="primary" @click="unfit">不合格</el-button>
        <el-button type="text" @click="uploadFile">立即下载</el-button>
      </div>
      <div class="pagination" v-else-if="this.processedState === 'OFFERED'">
        <el-button plain @click="back">返回</el-button>
        <el-button type="primary" @click="employed">确认入职</el-button>
      </div>
      <div class="pagination" v-else>
        <el-button plain @click="back">返回</el-button>
        <el-button type="primary" @click="uploadFile">立即下载</el-button>
      </div>
    </div>
    <!-- <div class="resume-nav">
      <div>操作日志</div>
    </div>
    <div class="resume-seconds">
      <el-timeline :reverse="reverse" style="margin:20px 0 0 0">
        <el-timeline-item
          v-for="(activity, index) in activities"
          :key="index"
          :timestamp="activity.timestamp"
        >{{activity.content}}</el-timeline-item>
      </el-timeline>
    </div>-->
  </div>
</template>
<script>
import qs from "qs";
import Cookies from "js-cookie";
export default {
  data() {
    return {
      ms: false,
      linktitle: "复制链接",
      textlink: "service@163.com",
      dialogVisible: false,
      centerDialogVisible: false,
      centerDialogVisibles: false,
      dialogVisibledit: false,
      positionId: "",
      oralTime: "",
      resumeId: "",
      resumeIds: "",
      companyAddress: "",
      resumeIdes: "",
      resumeDeta: {},
      resinfo: {},
      addressList: "",
      processedState: "",
      activities: [
        {
          content: "活动按期开始",
          timestamp: "2018-04-15"
        },
        {
          content: "通过审核",
          timestamp: "2018-04-13"
        },
        {
          content: "创建成功",
          timestamp: "2018-04-11"
        }
      ],
      ruleForm: {
        companyName: "",
        oralTime: "",
        positions: "",
        dalary: "",
        address: "",
        user: "",
        phone: "",
        desc: ""
      },
      rulesQuiz: {
        oralTime: [
          { required: true, message: "请选择面试时间", trigger: "blur" }
        ],
        phone: [
          { required: true, message: "请填写面试联系方式", trigger: "change" },
          {
            pattern: /^[1][3578][0-9]{9}$/,
            message: "请输入正确的手机号",
            trigger: ["change", "blur"]
          }
        ],
        address: [
          { required: true, message: "请选择面试地址", trigger: "change" }
        ],
        user: [
          { required: true, message: "请填写面试联系人", trigger: "change" }
        ]
      },
      arrResume: "",
      id: ""
    };
  },
  methods: {
    copy() {
      var clipboard = new Clipboard(".tabs-read");
      clipboard.on("success", e => {
        this.linktitle = "复制成功";
        // 释放内存
        setTimeout(() => {
          this.linktitle = "复制链接";
        }, 3000);
        clipboard.destroy();
      });
      clipboard.on("error", e => {
        // 不支持复制
        console.log("该浏览器不支持自动复制");
        // 释放内存
        clipboard.destroy();
      });
    },
    //确定面试
    transmit() {
      let params = {
        companyName: this.ruleForm.companyName,
        contactName: this.ruleForm.user,
        contactPhone: this.ruleForm.phone,
        interviewAddressId: this.ruleForm.address,
        interviewRemark: this.ruleForm.desc,
        interviewTime: this.ruleForm.oralTime.getTime(),
        positionName: this.ruleForm.positions,
        positionSubmittedId: null,
        resumeId: this.transId,
        salary: this.ruleForm.dalary
      };
      this.$http
        .post(`/business-core/position/${this.positionId}/resumes/info`, params)
        .then(res => {
          if (res.data.code === "200") {
            this.centerDialogVisibles = false;
            this.centerDialogVisible = false;
            let completedPercentMax;
            let completedPercentMin;
            switch (this.form.completedPercent) {
              case "1":
                completedPercentMin = 90;
                completedPercentMax = null;
                break;
              case "2":
                completedPercentMin = 80;
                completedPercentMax = 90;
                break;
              case "3":
                completedPercentMin = 70;
                completedPercentMax = 80;
                break;
              case "4":
                completedPercentMin = 60;
                completedPercentMax = 70;
                break;
              case "5":
                completedPercentMin = 50;
                completedPercentMax = 60;
                break;
              case "6":
                completedPercentMin = null;
                completedPercentMax = 50;
                break;
              default:
                completedPercentMin = null;
                completedPercentMax = null;
                break;
            }
            if (this.form.time.length !== 0) {
              this.startTime = this.form.time[0].getTime();
              this.endTime = this.form.time[1].getTime();
            } else {
              this.startTime = null;
              this.endTime = null;
            }
            this.params = {
              city: this.form.city[0] ? this.form.city[0] : null,
              completedPercentMax: completedPercentMax,
              completedPercentMin: completedPercentMin,
              jobSearchStatusCode:
                this.formInline.state === ""
                  ? null
                  : Number(this.formInline.state),
              pageNum: 1,
              pageSize: 10,
              processedState: this.processedState,
              province: this.form.city[1] ? this.form.city[1] : null,
              sortBy: null,
              sortOrder: null,
              submittedTimeEnd: this.endTime,
              submittedTimeStart: this.startTime
            };
            this.$http
              .post(
                `/business-core/position/${this.positionID}/resumes/list`,
                this.params
              )
              .then(res => {
                let response = res.data.data.list;
                if (res.data.code == "200") {
                  this.tableData = response;
                  if (this.processedState === "TO_PROCESS") {
                    this.page.total = res.data.data.total;
                  } else if (this.processedState === "PROCESSING") {
                    this.page1.total = res.data.data.total;
                  } else if (this.processedState === "OFFERED") {
                    this.page2.total = res.data.data.total;
                  } else {
                    this.page3.total = res.data.data.total;
                  }
                } else {
                }
              })
              .catch(error => {});
          } else {
          }
        })
        .catch(error => {});
    },
    //发送面试邀请
    submitForm(formName) {
      this.$refs[formName].validate(valid => {
        if (valid) {
          this.centerDialogVisible = true;
        } else {
          console.log("error submit!!");
          return false;
        }
      });
    },
    //预览
    preview() {
      this.dialogVisibledit = true;
      this.oralTime = this.ruleForm.oralTime.getTime();
      this.$http
        .get(`/business-core/companies/addresses/${this.ruleForm.address}`)
        .then(res => {
          if (res.data.code == "200") {
            this.companyAddress = res.data.data.detail;
          } else {
          }
        })
        .catch(error => {});
    },
    //列出公司下所有地址
    address() {
      this.$http
        .get(`/business-core/companies/addresses`)
        .then(res => {
          if (res.data.code == "200") {
            this.addressList = res.data.data;
          } else {
          }
        })
        .catch(error => {});
    },
    //面试
    info() {
      console.log(this.resinfo.companyName);
      this.centerDialogVisibles = true;
      this.ruleForm.companyName = this.resinfo.companyName;
      this.ruleForm.positions = this.resinfo.positionName;
      this.ruleForm.dalary = this.resinfo.salary;
      this.transId = this.resinfo.id;
    },
    uploadFile() {
      // let params = {
      //   resumeIds: this.arrResume
      // };
      // let resumeList = qs.stringify(
      //   { resumeIds: this.arrResume },
      //   { arrayFormat: "repeat" }
      // );
      this.dialogVisible = true;
      this.$local
        .get(`/business-core/resumes/download/${this.positionId}/${this.id}`, {
          responseType: "blob"
        })
        .then(res => {
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
    //不合格
    unfit(tab) {
      this.$http
        .put(
          `/business-core/position/${this.positionId}/resumes/${this.resumeId}/unfit`
        )
        .then(res => {
          let response = res.data.data;
          if (res.data.code == "200") {
            this.$router.go(-1);
          } else {
          }
        })
        .catch(error => {});
    },
    //录用
    offer(tab) {
      this.$http
        .put(
          `/business-core/position/${this.positionId}/resumes/${this.resumeId}/offer`
        )
        .then(res => {
          let response = res.data.data;
          if (res.data.code == "200") {
            this.$router.go(-1);
          } else {
          }
        })
        .catch(error => {});
    },
    //确认入职
    employed(tab) {
      this.$http
        .put(
          `/business-core/position/${this.positionId}/resumes/${this.resumeId}/employed`
        )
        .then(res => {
          let response = res.data.data;
          if (res.data.code == "200") {
            this.$router.go(-1);
          } else {
          }
        })
        .catch(error => {});
    },
    //返回
    back() {
      this.$router.go(-1); //返回上一层
    },
    resumeDetail() {
      this.$http
        .get(`/business-core/resumes/${this.resumeId}`)
        .then(res => {
          let response = res.data.data;
          if (res.data.code == "200") {
            this.resumeDeta = response;
          } else {
          }
        })
        .catch(error => {});
    },
    resumeDetails() {
      this.$http
        .get(`/business-core/platformTalentPool/databases/${this.resumeIds}`)
        .then(res => {
          let response = res.data.data;
          if (res.data.code == "200") {
            this.resumeDeta = response;
          } else {
          }
        })
        .catch(error => {});
    },
    resumeDetailes() {
      this.$http
        .get(`/business-core/CorporateTalentPool/databases/${this.resumeIdes}`)
        .then(res => {
          let response = res.data.data;
          if (res.data.code == "200") {
            this.resumeDeta = response;
          } else {
          }
        })
        .catch(error => {});
    }
  },
  created() {
    let token = Cookies.get("Btoken");
    if (this.$route.query.resinfo) {
      let list = decodeURIComponent(this.$route.query.resinfo);
      this.resinfo = JSON.parse(list);
    } else if (this.$route.query.positionId) {
      this.positionId = this.$route.query.positionId;
    } else if (this.$route.query.resumeId) {
      this.resumeId = this.$route.query.resumeId;
    } else if (this.$route.query.resumeIds) {
      this.resumeIds = this.$route.query.resumeIds;
    } else if (this.$route.query.resumeIdes) {
      this.resumeIdes = this.$route.query.resumeIdes;
    } else if (this.$route.query.processedState) {
      this.processedState = this.$route.query.processedState;
    }
    this.address();
    if (token) {
      if (this.resumeId) {
        this.resumeDetail();
        this.arrResume = this.resumeId;
        this.id = this.resumeId;
        // this.arrResume.push(this.resumeId);
      } else if (this.resumeIds) {
        this.resumeDetails();
        this.arrResume = this.resumeIds;
        this.id = this.resumeIds;
        // this.arrResume.push(this.resumeIds);
      } else {
        this.resumeDetailes();
        this.arrResume = this.resumeIdes;
        this.id = this.resumeIdes;
        // this.arrResume.push(this.resumeIdes);
      }
    } else {
      this.$notify.info({
        title: "消息",
        message: "登陆超时，请重新登录"
      });
      this.$router.push({ path: "/login" });
    }
  },
  filters: {
    level(level) {
      var a;
      switch (level) {
        case "NORMAL":
          a = "一般";
          break;
        case "GOOD":
          a = "良好";
          break;
        case "PROFICIENT":
          a = "熟练";
          break;
        case "MASTER":
          a = "精通";
          break;
      }
      return a;
    }
  }
};
</script>
<style lang="stylus" scoped>
.personal-title {
  .titles {
    font-family: PingFangSC-Regular;
    color: #222222;
    font-size: 18px;
  }

  .content {
    font-family: PingFangSC-Regular;
    color: #5A5A5A;
    font-size: 16px;
    margin: 12px 0 0 0;

    span {
      font-family: PingFangSC-Regular;
      color: #373737;
      font-size: 16px;
    }
  }

  .aside {
    font-family: PingFangSC-Regular;
    color: #4C4C4C;
    font-size: 16px;
    margin: 20px 0 0 0;

    span {
      font-family: PingFangSC-Regular;
      color: #02b9b8;
      font-size: 16px;
    }
  }

  .foot {
    font-family: PingFangSC-Medium;
    color: #222222;
    font-size: 16px;
    margin: 12px 0 0 0;

    span {
      font-family: PingFangSC-Regular;
      color: #02b9b8;
      font-size: 16px;
    }
  }

  .el-icon-close:before {
    content: '\e6db';
    font-size: 30px;
    line-height: 20px;
  }
}

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

.resume-nav {
  width: 100%;
  height: 50px;
  background: #FAFAFA;
  display: flex;
  flex-direction: row;
  justify-content: space-between;
  line-height: 50px;
  margin: 20px 0 0 0;

  div {
    margin: 0 0 0 20px;
    color: #02B9B8;
  }
}

.resume-seconds {
  width: 100%;
  height: auto;
  background: #ffffff;
  border: 1px solid rgba(244, 244, 244, 1);
  box-shadow: 0px 2px 10px 0px rgba(245, 245, 245, 1);
}

.resume {
  display: flex;
  flex-direction: row;

  .infor {
    margin: 22px 0 0 20px;
  }

  .infor div {
    padding: 8px 0 0 0;
    font-size: 18px;
    color: #6C6C6C;
  }

  .infor div:nth-child(2) span {
    padding: 0 0 0 10px;
  }

  .infor div:nth-child(3) span {
    padding: 0 0 0 10px;
  }

  .block {
    margin: 25px 0 0 25px;
  }
}

.line {
  width: 98%;
  border: 0.5px solid #e5e5e5;
  margin: 20px 0 0 1%;
}

.jotension {
  font-size: 15px;
  display: flex;
  flex-direction: column;
  margin: 0 0 20px 0;

  .jotension-content {
    margin: 20px 0 0 35px;
    display: flex;
    flex-direction: row;
  }

  .jotension-content div:nth-child(1) {
    font-size: 16px;
    width: 600px;
    color: #5A5A5A;
  }

  .jotension-content div:nth-child(2) {
    font-size: 16px;
    width: 300px;
    color: #5A5A5A;
  }
}

.title {
  font-size: 18px;
  font-weight: bold;
  margin: 25px 0 0 35px;
  color: #373737;
}

.train-content {
  margin: 20px 0 0 35px;
  display: flex;
  flex-direction: row;
  font-size: 16px;
  color: #373737;
}

.train-content div:nth-child(1) {
  width: 150px;
  font-size: 16px;
  color: #373737;
}

.train-content div:nth-child(2) {
  margin: 0 0 0 0;
  width: 150px;
  font-size: 16px;
  color: #373737;
}

.train-content div:nth-child(3) {
  margin: 0 0 0 0;
  font-size: 16px;
  color: #373737;
}

.train-content div:nth-child(4) {
  margin: 0 0 0 100px;
  font-size: 16px;
  color: #373737;
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