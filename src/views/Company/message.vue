<template>
  <div class="enterprise-info-container">
    <el-dialog title :visible.sync="centerDialogVisible" :show-close="false" width="23%" center>
      <div style="text-align:center">
        <div class="loading">
          <i style="font-size:60px;color:#faad14;" class="el-icon-warning"></i>
        </div>
        <div class="loading-text">是否保存修改？</div>
        <div style="text-align:center">
          <el-button
            style="height:40px;line-height:0px;margin:35px 0 0 0"
            plain
            @click="centerDialogVisible = false"
          >取消</el-button>
          <el-button
            style="height:40px;line-height:0px;margin:35px 0 0 30px"
            type="primary"
            @click="centerDialogVisible = false"
          >保存</el-button>
        </div>
      </div>
    </el-dialog>
    <div class="body">
      <!-- <div class="head">
        <p>企业信息完整度</p>
        <el-progress :text-inside="true" :stroke-width="15" percentage="0"></el-progress>
      </div>-->
      <div class="form">
        <p class="header">
          <span class="title">基本信息</span>
          <el-button
            class="edit-btn main"
            @click="editEnterpriseRegisterInfo"
            v-if="enterpriseInfoEditMode"
          >编辑</el-button>
        </p>
        <div class="display" v-if="enterpriseInfoEditMode">
          <ul>
            <li>
              <span>企业名称：</span>
              <span class="spanSecond">{{companyInfo.fullName}}</span>
            </li>
            <li>
              <span>企业简称：</span>
              <span class="spanSecond">{{companyInfo.shortName}}</span>
            </li>
            <li>
              <span>企业规模：</span>
              <span class="spanSecond">{{companyInfo.size}}</span>
            </li>
            <li>
              <span>企业性质：</span>
              <span class="spanSecond">{{companyInfo.nature}}</span>
            </li>
            <li>
              <span>所属行业：</span>
              <span class="spanSecond">{{companyInfo.industrySecondary}}</span>
            </li>
            <li>
              <span>企业地点：</span>
              <span class="spanSecond">{{companyInfo.address.detail}}</span>
            </li>
            <li>
              <span>企业介绍：</span>
              <span class="spanSecond">{{companyInfo.description}}</span>
            </li>
            <li>
              <span>企业 LOGO：</span>
              <span>
                <img class="logo" :src="this.companyInfo.logoUrl" />
              </span>
            </li>
          </ul>
        </div>
        <el-form
          class="form-container"
          ref="companyInfo"
          :rules="companyInfoFormRules"
          :model="companyInfo"
          label-width="140px"
          v-else
        >
          <el-form-item label="企业名称" prop="fullName">
            <span>{{ companyInfo.fullName }}</span>
          </el-form-item>
          <el-form-item label="企业简称" prop="shortName">
            <el-input style="width:240px" v-model="companyInfo.shortName" placeholder="请输入企业简称"></el-input>
          </el-form-item>
          <el-form-item label="企业规模" prop="size">
            <el-select style="width:240px" v-model="companyInfo.size" placeholder="请选择">
              <el-option
                v-for="item in this.optionList.companySize"
                :key="item.code"
                :label="item.tag"
                :value="item.tag"
              ></el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="企业性质" prop="nature">
            <el-select style="width:240px" v-model="companyInfo.nature" placeholder="请选择企业性质">
              <el-option
                v-for="item in optionsNature"
                :key="item.code"
                :label="item.tag"
                :value="item.tag"
              ></el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="所属行业" prop="industry">
            <el-cascader
              style="width:240px"
              :props="props"
              :options="industryList"
              v-model="companyInfo.industry"
              placeholder="请选择所属行业"
            ></el-cascader>
          </el-form-item>
          <el-form-item label="企业地址" prop="city">
            <el-cascader
              v-model="companyInfo.city"
              style="width:240px"
              :props="props"
              :options="cityList"
            ></el-cascader>
          </el-form-item>
          <el-form-item label="企业地址">
            <el-input
              type="textarea"
              style="width:500px;"
              :rows="4"
              maxlength="80"
              show-word-limit
              v-model="companyInfo.address.detail"
              placeholder="请输入内容"
            ></el-input>
          </el-form-item>
          <el-form-item label="企业介绍" prop="description">
            <el-input
              style="width:500px"
              type="textarea"
              maxlength="300"
              show-word-limit
              :rows="4"
              placeholder="请输入内容"
              v-model="companyInfo.description"
            ></el-input>
          </el-form-item>
          <el-form-item label="企业 LOGO">
            <el-upload
              class="avatar-upload"
              :action="uploadUrl"
              :data="uploadData"
              :headers="myHeaders"
              style="margin-left:0px"
              :show-file-list="false"
              :on-success="handleAvatarSuccess"
              :on-error="handleAvatarError"
            >
              <img v-if="imageUrl" :src="imageUrl" class="avatar" />
              <!-- <i
                v-else
                style="border:1px solid #dcdfe6;margin:0 200px 0 0"
                class="el-icon-plus avatar-uploader-icon"
              ></i>-->
              <div class="el-upload__tip">支持图片格式：png、jpg、jpeg，最大不超过 3M。</div>
              <div class="el-upload__tip">为了尽快通过审核，请上传真实合法且清晰的执照图片。</div>
            </el-upload>
            <!-- <img
              ref="preview-logo"
              class="logo"
              :src="companyInfo.logoUrl"
              v-if="companyInfo.logoUrl"
            />
            <el-button size="small" type="primary">点击更新</el-button>
            <div class="el-upload__tip">支持图片格式：png、jpg、jpeg，最大不超过 3M。</div>
            <div class="el-upload__tip">为了尽快通过审核，请上传真实合法且清晰的执照图片。</div>
            <input class="image-input" ref="input" type="file" accept="image/*" />
            <el-dialog
              title="裁切图片"
              :visible.sync="lOGOUploadModalVisible"
              :close-on-click-modal="false"
              :close-on-press-escape="false"
              width="30%"
            >
              <vue-cropper ref="cropper" :src="imgSrc" :aspectRatio="1"></vue-cropper>
              <span slot="footer" class="dialog-footer">
                <el-button>重新选择</el-button>
                <el-button type="primary" @click="cropImage">确定</el-button>
              </span>
            </el-dialog>-->
          </el-form-item>
          <div class="operations">
            <el-button type="primary" class="main" @click="updateCompany('companyInfo')">保存</el-button>
            <el-button @click="clearAndReloads">取消</el-button>
          </div>
        </el-form>
        <span class="line"></span>
        <p class="header">
          <span class="title">企业审核信息</span>
          <el-button
            class="edit-btn main"
            @click="editEnterpriseInfo"
            v-if="enterpriseRegisterInfoEditMode"
          >编辑</el-button>
        </p>
        <div class="display" v-if="enterpriseRegisterInfoEditMode">
          <ul>
            <li>
              <span>企业名称：</span>
              <span class="spanSecond">{{this.companyName}}</span>
            </li>
            <li>
              <span>企业注册地：</span>
              <span class="spanSecond">{{ this.registeredAddress}}</span>
            </li>
            <li>
              <span>统一社会信用代码：</span>
              <span class="spanSecond">{{ this.uniformSocialCreditCode}}</span>
            </li>
            <li>
              <span>企业类别：</span>
              <span class="spanSecond">{{ this.enterpriseForm}}</span>
            </li>
            <li>
              <span>证件原件照片：</span>
              <span>
                <img class="logo" :src="this.accessUrl" />
              </span>
            </li>
          </ul>
        </div>
        <el-form
          class="form-container"
          ref="companyInfos"
          :rules="auditInfoFormRules"
          :model="companyInfos"
          label-width="140px"
          v-else
        >
          <el-form-item label="企业名称" prop="companyName">
            <span style="width:240px">{{companyInfos.companyName}}</span>
          </el-form-item>
          <el-form-item label="企业注册地" prop="registeredAddress">
            <el-input
              style="width:240px"
              v-model="companyInfos.registeredAddress"
              placeholder="请输入企业注册地（省份、城市）"
            ></el-input>
          </el-form-item>
          <el-form-item label="统一社会信用代码" prop="uniformSocialCreditCode">
            <el-input
              style="width:240px"
              maxlength="18"
              v-model="companyInfos.uniformSocialCreditCode"
              placeholder="请输入与企业证件材料一致的代码"
            ></el-input>
          </el-form-item>
          <el-form-item label="企业类别" prop="enterpriseForm">
            <el-cascader
              style="width:240px"
              :props="props"
              :options="list"
              v-model="companyInfos.enterpriseForm"
              placeholder="请选择企业类型"
            ></el-cascader>
          </el-form-item>
          <el-form-item label="上传证件原件照片" prop="file">
            <el-upload
              class="upload"
              :action="uploadUrl"
              :file-list="auditTempFile"
              :on-success="dealWithUploadLicense"
              :on-error="handleAvatarError"
              :with-credentials="true"
              :data="uploadDatas"
              :headers="myHeaders"
              :limit="1"
              list-type="picture"
            >
              <el-button size="small" type="primary">点击上传</el-button>
              <div class="el-upload__tip" slot="tip">支持图片格式：png、jpg、jpeg、gif，最大不超过 3M。</div>
              <div
                class="el-upload__tip"
                slot="tip"
              >提示：确保企业名称与提交审核公司一致；如为复印件、黑白扫描件，需加盖企业公章；不可使用屏幕截图或翻拍照片提交审核；上传图片不可有与银领人才网无关的标注或水印；不可使用电子版营业执照提交审核；所上传证照照片，信息需清晰完整；</div>
              <div class="el-upload__tip warning" slot="tip">企业营业执照仅用审核，不会向第三方透露，请放心上传！</div>
            </el-upload>
          </el-form-item>
          <div class="operations">
            <el-button type="primary" class="main" @click="updateCompanyVerify('companyInfos')">保存</el-button>
            <el-button @click="clearAndReload">取消</el-button>
          </div>
        </el-form>
      </div>
    </div>
  </div>
</template>
<script>
import Cookies from "js-cookie";
let token = Cookies.get("Btoken");
import industry from "../../assets/industry.json";
import city from "../../assets/city.json";
import option from "../../assets/option.json";
import list from "../../assets/list.json";
import timeUtil from "../../timeUtil.js";
export default {
  name: "home",
  data() {
    return {
      myHeaders: { "Auth-Token": window.sessionStorage.getItem("Btoken") },
      uploadData: {
        label: "company-logo"
      },
      uploadDatas: {
        label: "company-license"
      },
      list: [],
      lOGOUploadModalVisible: false,
      centerDialogVisible: false,
      enterpriseInfoEditMode: true,
      enterpriseRegisterInfoEditMode: true,
      props: {
        value: "tag",
        label: "tag",
        children: "children"
      },

      imgSrc: "",
      cityList: [],
      industryList: [],
      optionList: [],
      companyInfo: {
        completedPercent: 0,
        address: {
          detail: ""
        },
        fullName: "",
        industry: [],
        nature: "",
        shortName: "",
        size: "",
        addressId: "",
        logoUrl: "",
        city: [],
        description: ""
      },
      companyInfos: {
        companyName: "",
        enterpriseForm: "",
        accessUrl: "",
        registeredAddress: "",
        uniformSocialCreditCode: ""
      },
      optionsNature: [
        {
          code: 0,
          tag: "国有企业"
        },
        {
          code: 1,
          tag: "民营企业"
        },
        {
          code: 2,
          tag: "合资企业"
        },
        {
          code: 3,
          tag: "外资企业"
        },
        {
          code: 4,
          tag: "事业单位"
        }
      ],
      companyInfoFormRules: {
        fullName: [
          { required: true, message: "请输入公司全称", trigger: "blur" },
          { min: 0, max: 36, message: "长度在 0 到 36 个字", trigger: "blur" }
        ],
        shortName: [
          { required: true, message: "请输入公司简称", trigger: "blur" },
          { min: 0, max: 5, message: "长度在 0 到 5 个字", trigger: "blur" }
        ],
        size: [{ required: true, message: "请选择企业规模", trigger: "blur" }],
        nature: [
          { required: true, message: "请选择企业性质", trigger: "blur" }
        ],
        industry: [
          { required: true, message: "请选择所属行业", trigger: "blur" }
        ],
        city: [{ required: true, message: "请选择企业地址", trigger: "blur" }],
        detail: [
          { required: true, message: "请填写企业地址", trigger: "blur" },
          { min: 0, max: 80, message: "长度在 0 到 80 个字", trigger: "blur" }
        ],
        description: [
          { required: true, message: "请输入企业介绍", trigger: "blur" },
          { min: 0, max: 300, message: "长度在 0 到 300 个字", trigger: "blur" }
        ],
        file: [{ required: true, message: "请上传公司照片", trigger: "blur" }]
      },
      companyId: "",
      imageUrl: "",
      file: "",
      files: "",
      companyName: "",
      enterpriseForm: "",
      accessUrl: "",
      registeredAddress: "",
      uniformSocialCreditCode: ""
    };
  },
  methods: {
    handleAvatarSuccess(res, file) {
      console.log(URL.createObjectURL(file.raw));
      this.imageUrl = URL.createObjectURL(file.raw);
      this.file = res.data;
    },
    handleAvatarError(err, file, fileList) {
      this.$notify.info({
        title: "消息",
        message: "图片上传失败，请重新上传"
      });
    },
    dealWithUploadLicense(res, file) {
      // this.imageUrl = URL.createObjectURL(file.raw);
      this.files = res.data;
    },
    //获取公司简讯
    brief() {
      this.$http
        .get("/business-core/companyes/brief")
        .then(res => {
          if (res.data.code == "200") {
            this.companyId = res.data.data.id;
          } else {
          }
        })
        .catch(error => {});
    },
    //获取公司详情
    companyDetail() {
      this.$http
        .get("/business-core/companyes")
        .then(res => {
          let response = res.data.data;
          if (res.data.code == "200") {
            this.companyInfo = response;
            this.companyInfo.industry = [
              response.industryFirst,
              response.industrySecondary
            ];
            this.companyInfo.city = [
              response.address.province,
              response.address.city,
              response.address.district
            ];

            // this.companyInfo.address.companyDetail = response.address.detail;
            this.companyInfo.logoUrl = response.logo.accessUrl;
            this.imageUrl = response.logo.accessUrl;
            this.companyInfo.addressId = response.addressId;
          } else {
          }
        })
        .catch(error => {});
    },
    //获取公司审核信息
    companyVerify() {
      this.$http
        .get("/business-core/companyes/cert")
        .then(res => {
          let response = res.data.data;
          if (res.data.code == "200") {
            (this.companyName = response.companyName),
              (this.enterpriseForm = response.enterpriseForm),
              (this.registeredAddress = response.registeredAddress),
              (this.uniformSocialCreditCode = response.uniformSocialCreditCode);
            this.accessUrl = response.cert.accessUrl;
          } else {
          }
        })
        .catch(error => {});
    },
    //更新公司审核信息
    updateCompanyVerify(formName) {
      this.$refs[formName].validate(valid => {
        if (valid) {
          let params = {
            cert: this.files ? this.files : null,
            enterpriseForm: this.companyInfos.enterpriseForm instanceof Array?(this.companyInfos.enterpriseForm[1]?this.companyInfos.enterpriseForm[1]:this.companyInfos.enterpriseForm[0]):this.companyInfos.enterpriseForm,
            enterpriseFormCode: null,
            registeredAddress: this.companyInfos.registeredAddress,
            uniformSocialCreditCode: this.companyInfos.uniformSocialCreditCode
          };
          this.$http
            .put("/business-core/companyes/cert", params)
            .then(res => {
              let response = res.data.data;
              if (res.data.code == "200") {
                this.enterpriseRegisterInfoEditMode = true;
                this.companyVerify();
                this.$message({
                  message: res.data.message,
                  type: "success"
                });
              } else {
              }
            })
            .catch(error => {});
        } else {
          console.log("error submit!!");
          return false;
        }
      });
    },
    //更新公司
    updateCompany(formName) {
      this.$refs[formName].validate(valid => {
        if (valid) {
          let params = {
            addressId: this.companyInfo.addressId,
            companyAddressBody: {
              city: this.companyInfo.city[1],
              detail: this.companyInfo.address.detail,
              district: this.companyInfo.city[2],
              province: this.companyInfo.city[0]
            },
            companyId: this.companyId,
            description: this.companyInfo.description,
            fullName: this.companyInfo.fullName,
            industryCode: null,
            industryFirst: this.companyInfo.industry[0],
            industrySecondary: this.companyInfo.industry[1],
            logo: this.file ? this.file : null,
            nature: this.companyInfo.nature,
            natureCode: null,
            shortName: this.companyInfo.shortName,
            size: this.companyInfo.size,
            sizeCode: null
          };
          this.$http
            .put("/business-core/companyes", params)
            .then(res => {
              let response = res.data.data;
              if (res.data.code == "200") {
                this.enterpriseInfoEditMode = true;
                this.companyDetail();
                this.$message({
                  message: res.data.message,
                  type: "success"
                });
              } else {
              }
            })
            .catch(error => {});
        } else {
          console.log("error submit!!");
          return false;
        }
      });
    },
    editEnterpriseRegisterInfo() {
      this.enterpriseInfoEditMode = false;
      // if (this.enterpriseInfoEditMode) {
      //   this.$notify({
      //     title: "警告",
      //     message: "请先保存企业基本信息！",
      //     type: "warning"
      //   });
      // } else {
      //   this.enterpriseInfoEditMode = false;
      // }
    },
    editEnterpriseInfo() {
      this.enterpriseRegisterInfoEditMode = false;
      this.companyInfos.companyName = this.companyName;
      this.companyInfos.enterpriseForm = this.enterpriseForm;
      this.companyInfos.registeredAddress = this.registeredAddress;
      this.companyInfos.uniformSocialCreditCode = this.uniformSocialCreditCode;
      console.log(this.companyInfos.enterpriseForm)
      // this.companyInfo.companyName = this.companyName
      // if (this.enterpriseRegisterInfoEditMode) {
      //   this.$notify({
      //     title: "警告",
      //     message: "请先保存企业审核信息！",
      //     type: "warning"
      //   });
      // } else {
      //   this.enterpriseRegisterInfoEditMode = false;
      // }
    },
    clearAndReload() {
      this.enterpriseRegisterInfoEditMode = true;
    },
    clearAndReloads() {
      this.enterpriseInfoEditMode = true;
      this.companyDetail();
    }
  },
  computed: {
    uploadUrl() {
      return "/api/v3/file-service/files/upload";
    }
  },
  created() {
    let token = Cookies.get("Btoken");
    this.cityList = city.data;
    this.industryList = industry.data;
    this.optionList = option.data;
    this.list = list.data;
    if (token) {
      this.brief();
      this.companyDetail();
      this.companyVerify();
    } else {
      this.$notify.info({
        title: "消息",
        message: "登陆超时，请重新登录"
      });
      this.$router.push({ path: "/login" });
    }
  }
};
</script>
<style lang="stylus" scoped>
.loading-text {
  font-family: PingFangSC-Medium;
  color: #111111;
  font-size: 20px;
  margin: 20px 0 0 0;
}

.enterprise-info-container {
  display: flex;
  flex-direction: column;
  margin-top: 0px;

  .nav {
    position: relative;
    flex: 0 0 70px;
  }

  .body {
    display: flex;
    width: 100%;
    justify-content: center;
    flex-direction: column;
    flex: 1;
    position: relative;
    background-color: white;
    margin: auto;

    .inline-top-item {
      margin-bottom: 10px;
    }

    .head {
      width: 240px;
      text-align: left;
      font-size: 14px;
      margin: 20px 0 20px 15px;
    }

    .form {
      padding: 10px;

      .form-container {
        .operations {
          text-align: right;
        }

        .image-input {
          display: none;
        }
      }

      .display {
        ul {
          list-style: none;
          text-align: left;
          font-size: 16px;
          padding: 0;

          li {
            margin: 40px 0;
            display: flex;

            span:first-child {
              flex: 0 0 150px;
              text-align: right;
            }

            .spanSecond {
              font-size: 14px;
              margin: 3px 0 0 0;
            }
          }
        }
      }

      img {
        max-height: 100px;
        margin-right: 20px;
        vertical-align: bottom;
      }

      .line {
        width: 100%;
        height: 1px;
        display: inline-block;
        background: lightgrey;
        margin: 20px 0;
      }

      button.full {
        width: 100%;
      }

      p {
        display: flex;
        flex-direction: row;
        margin-bottom: 20px;

        &.header {
          justify-content: space-between;
          margin: 10px 0 30px 5px;
          font-weight: bold;

          .title {
            display: inline-block;
            vertical-align: middle;
            margin-right: 3px;
            line-height: 38px;
          }
        }

        span {
          font-size: 22px;
        }
      }
    }
  }
}
</style>