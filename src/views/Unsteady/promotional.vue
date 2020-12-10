<template>
  <div class="unsteady">
    <div class="title">
      <div class="title-nav">基本信息</div>
      <div class="title-content" @click="attention()">发布活动须知 ></div>
      <el-dialog title width="23%" :visible.sync="dialogDrag" style="border-radius:5px;">
        <div>
          <el-upload
            class="upload-demo"
            :action="uploadUrl"
            style="margin:0 0 20px 0"
            drag
            :data="uploadfile"
            :headers="myHeaders"
            :show-file-list="false"
            :on-success="handleVideoSuccess"
          >
            <div>
              <i class="el-icon-upload"></i>
              <div class="el-upload__text">
                将文件拖到此处，或
                <em>点击上传</em>
                <div class="el-upload__tip" slot="tip">支持DOC、DOCX、PDF、JPG、PNG格式，文件大小需小于10M。</div>
              </div>
            </div>
          </el-upload>
          <div class="dialogResume" v-if="videoFlag == true">
            <div>
              <i class="el-icon-paperclip"></i>
              <span style="margin:0 0 0 5px">123</span>
            </div>
            <div>
              <i class="el-icon-delete"></i>
            </div>
          </div>
        </div>
      </el-dialog>
    </div>
    <div v-if="unsteady">
      <div class="content">
        <el-form
          :model="unsteadyForm"
          :rules="rules"
          ref="unsteadyForm"
          label-width="130px"
          class="demo-ruleForm"
        >
          <el-form-item label="活动名称" prop="unsteadyName">
            <el-input class="unsteadyName" v-model="unsteadyForm.unsteadyName"></el-input>
          </el-form-item>
          <el-form-item label="上传封面">
            <el-upload
              class="upload-demo"
              :action="uploadUrl"
              :on-success="dealWithUploadLicense"
              :data="uploadData"
              :headers="myHeaders"
              drag
              multiple
            >
              <img v-if="imageUrl" :src="imageUrl" class="avatar" />
              <div v-else>
                <i class="el-icon-upload"></i>
                <div class="el-upload__text">
                  将文件拖到此处，或
                  <em>点击上传</em>
                </div>
              </div>

              <div class="el-upload__tip" slot="tip">
                1、仅支持图片尺寸364*210
                jpg或png格式，且小于4M。
                2、精美封面有助于增加报名量，并有机会获得强力推荐！
              </div>
            </el-upload>
          </el-form-item>
          <el-form-item label="活动时间" prop="unsteadyTime">
            <el-date-picker
              v-model="unsteadyForm.unsteadyTime"
              type="datetimerange"
              :picker-options="expireTimeOption"
              range-separator="至"
              start-placeholder="开始日期"
              end-placeholder="结束日期"
            ></el-date-picker>
          </el-form-item>
          <el-form-item label="报名时间" prop="reportTime">
            <el-date-picker
              v-model="unsteadyForm.reportTime"
              type="datetimerange"
              :picker-options="expireTimeOption"
              range-separator="至"
              start-placeholder="开始日期"
              end-placeholder="结束日期"
            ></el-date-picker>
          </el-form-item>
          <el-form-item label="报名人数" prop="unsteadyNum">
            <el-input
              class="unsteadyNum"
              maxlength="1000"
              show-word-limit
              v-model="unsteadyForm.unsteadyNum"
            ></el-input>人
          </el-form-item>
          <el-form-item label="活动方式" prop="pattern">
            <el-radio-group v-model="unsteadyForm.pattern" class="pattern">
              <el-radio style="margin:10px 0 0 0" label="0">
                线上活动
                <span style="margin:0 0 0 10px;color:#aaaaaa">通过网络工具举办的线上活动</span>
              </el-radio>
              <el-radio style="margin:15px 0 0 0;" label="1">
                线下活动
                <span style="margin:0 0 0 10px;color:#aaaaaa">有具体活动地址的线下活动</span>
              </el-radio>
            </el-radio-group>
          </el-form-item>
          <el-form-item
            label="活动地址"
            prop="unsteadyAddress"
            class="unsteadyAddress"
            v-if="this.unsteadyForm.pattern === '1'"
          >
            <el-cascader
              v-model="unsteadyForm.unsteadyAddress"
              :props="props"
              :options="this.cityList"
            ></el-cascader>
            <el-input
              style="width:286px;margin:0 0 0 15px"
              v-model="unsteadyForm.unsteadyAddressDetail"
              placeholder="请输入详细地址"
            ></el-input>
          </el-form-item>
          <el-form-item label="活动详情" prop="unsteadyDetail" class="unsteadyDetail">
            <!-- <el-input
              type="textarea"
              placeholder="请输入内容"
              v-model="unsteadyForm.unsteadyDetail"
              maxlength="2000"
              style="width:759px"
              show-word-limit
            ></el-input>-->
            <!-- <div><i style="font-size:30px;position:absolute;top:40px;z-index:400;left:70px" class="el-icon-picture"></i></div> -->
            <div @click="previews()" style="width:42px;position:absolute;top:40px;z-index:400;left:57px;">
              <img
                style="width:20px;margin:9px 0 0 12px"
                src="../../assets/images/appraise-chuan.png"
              />
            </div>
            <editor
              style="border:1px solid red"
              id="tinymce"
              v-model="unsteadyForm.unsteadyDetail"
              :init="init"
            ></editor>
          </el-form-item>

          <div class="title">
            <div class="title-nav">其他设置</div>
          </div>
          <div class="demo-ruleForms">
            <el-form-item label="主办方联系信息" prop="unsteadyNames">
              <el-input
                placeholder="联系人姓名"
                style="width:160px"
                v-model="unsteadyForm.unsteadyNames"
              ></el-input>
            </el-form-item>
            <el-form-item label prop="unsteadyPhone">
              <el-input
                placeholder="联系人电话"
                style="width:160px"
                v-model="unsteadyForm.unsteadyPhone"
              ></el-input>
            </el-form-item>
            <el-form-item label prop="unsteadyEmail">
              <el-input
                placeholder="联系人邮箱"
                style="width:160px"
                v-model="unsteadyForm.unsteadyEmail"
              ></el-input>
            </el-form-item>
          </div>
          <el-form-item>
            <!-- <el-button style="margin:20px 0 20px 240px">预览</el-button> -->
            <el-button
              style="margin:20px 0 20px 320px"
              type="primary"
              @click="next('unsteadyForm')"
            >下一步</el-button>
          </el-form-item>
        </el-form>
      </div>
    </div>
    <div class="content" v-else>
      <el-dialog title="浏览表单" :visible.sync="dialogVisible" width="25%">
        <div
          style="margin:20px 0 0 55px"
          v-for="(item, index) in this.formAttributeBodies"
          :key="index"
        >
          {{item.chineseName}}
          <el-input
            placeholder="请填写"
            style="width:234px;margin:0 0 0 10px"
            v-model="checkForm[item.englishName]"
          ></el-input>
        </div>
        <span slot="footer" class="dialog-footer" style="margin:0 auto">
          <el-button type="primary" @click="dialogVisible = false">确 定</el-button>
        </span>
      </el-dialog>
      <div style="margin:10px 0 40px 30px">
        <el-checkbox-group v-model="checkList">
          <el-checkbox label="0">
            姓名
            <el-input
              class="checkedInput"
              :disabled="true"
              v-model="checkForm.surname"
              placeholder="报名用户的姓名或昵称"
            ></el-input>
          </el-checkbox>
          <el-checkbox label="1">
            手机
            <el-input
              class="checkedInput"
              :disabled="true"
              v-model="checkForm.phone"
              placeholder="报名用户的手机号码"
            ></el-input>
          </el-checkbox>
          <el-checkbox label="2">
            邮箱
            <el-input
              class="checkedInput"
              :disabled="true"
              v-model="checkForm.email"
              placeholder="报名用户的电子邮箱"
            ></el-input>
          </el-checkbox>
          <el-checkbox label="3">
            性别
            <el-input
              class="checkedInput"
              :disabled="true"
              v-model="checkForm.sex"
              placeholder="报名用户的性别"
            ></el-input>
          </el-checkbox>
          <el-checkbox label="4">
            年龄
            <el-input
              class="checkedInput"
              :disabled="true"
              v-model="checkForm.age"
              placeholder="报名用户的年龄"
            ></el-input>
          </el-checkbox>
          <el-checkbox label="5">
            职位
            <el-input
              class="checkedInput"
              :disabled="true"
              v-model="checkForm.position"
              placeholder="报名用户的职位"
            ></el-input>
          </el-checkbox>
          <el-checkbox label="6">
            学历
            <el-input
              class="checkedInput"
              :disabled="true"
              v-model="checkForm.record"
              placeholder="报名用户的学历"
            ></el-input>
          </el-checkbox>
          <el-checkbox label="7">
            学校
            <el-input
              class="checkedInput"
              :disabled="true"
              v-model="checkForm.school"
              placeholder="报名用户的毕业学校"
            ></el-input>
          </el-checkbox>
          <el-checkbox label="8">
            专业
            <el-input
              class="checkedInput"
              :disabled="true"
              v-model="checkForm.major"
              placeholder="报名用户的专业"
            ></el-input>
          </el-checkbox>
        </el-checkbox-group>
      </div>
      <el-button type="primary" @click="back()" style="margin:0 0 100px 290px">上一步</el-button>
      <el-button style="margin:0 0 100px 60px" @click="preview">预览</el-button>
      <el-button style="margin:0 0 100px 80px" type="primary" @click="submitForm()">发布</el-button>
      <div class="line"></div>
      <div class="footer">我们在48小时之内进行处理，审核通过将在应聘端活动中心进行展示</div>
    </div>
  </div>
</template>
<script>
import city from "../../assets/city.json";

import tinymce from "tinymce";
import Editor from "@tinymce/tinymce-vue";
import "tinymce/icons/default/icons.min.js";
import "tinymce/themes/silver/theme";
import "tinymce/plugins/textcolor";
import "tinymce/plugins/advlist";
import "tinymce/plugins/table";
import "tinymce/plugins/lists";
import "tinymce/plugins/paste";
import "tinymce/plugins/preview";
import "tinymce/plugins/fullscreen";
import "tinymce/plugins/anchor";
import "tinymce/plugins/autolink";
import "tinymce/plugins/autosave";
import "tinymce/plugins/code";
import "tinymce/plugins/codesample";
import "tinymce/plugins/colorpicker";
import "tinymce/plugins/contextmenu";
import "tinymce/plugins/directionality";
import "tinymce/plugins/emoticons";
import "tinymce/plugins/emoticons/js/emojis.min";
import "tinymce/plugins/image";
import "tinymce/plugins/hr";
import "tinymce/plugins/imagetools";
import "tinymce/plugins/insertdatetime";
import "tinymce/plugins/link";
import "tinymce/plugins/media";
import "tinymce/plugins/nonbreaking";
import "tinymce/plugins/noneditable";
import "tinymce/plugins/pagebreak";
import "tinymce/plugins/print";
import "tinymce/plugins/save";
import "tinymce/plugins/searchreplace";
import "tinymce/plugins/spellchecker";
import "tinymce/plugins/tabfocus";
import "tinymce/plugins/template";
import "tinymce/plugins/textpattern";
import "tinymce/plugins/visualblocks";
import "tinymce/plugins/visualchars";
import "tinymce/plugins/wordcount";

import Cookies from "js-cookie";
import { CodeToTag } from "../../cookie.js";
let token = Cookies.get("Btoken");
export default {
  components: { Editor },
  data() {
    return {
      dialogDrag: false,
      expireTimeOption: {
        disabledDate(date) {
          //disabledDate 文档上：设置禁用状态，参数为当前日期，要求返回 Boolean
          return date.getTime() < Date.now() - 24 * 60 * 60 * 1000;
        }
      },
      imageUrl: "",
      file: "",
      uploadfile: {
        label: "activity-content-file"
      },
      myHeaders: { "Auth-Token": window.sessionStorage.getItem("Btoken") },
      uploadData: {
        label: "activity-poster"
      },
      init: {
        init_instance_callback: function(editor) {},

        menubar: false, // 禁用菜单栏
        branding: false, // 隐藏右下角技术支持
        elementpath: false, // 隐藏底栏的元素路径
        paste_data_images: true, // 允许粘贴图像
        font_formats:
          "微软雅黑=Microsoft YaHei,Helvetica Neue,PingFang SC,sans-serif;苹果苹方=PingFang SC,Microsoft YaHei,sans-serif;宋体=simsun,serif",
        fontsize_formats:
          "12px 14px 16px 18px 20px 22px 24px 26px 28px 30px 32px 34px 36px 38px 40px 50px 60px 70px 80px 90px 100px 120px 140px 160px 180px 200px",
        language_url: "./tinymce/langs/zh_CN.js",
        language: "zh_CN",
        skin_url: "./tinymce/skins/ui/oxide",
        plugins:
          "link lists code table colorpicker image textcolor wordcount contextmenu",
        // toolbar:
        //     `bold italic underline strikethrough | fontsizeselect | forecolor backcolor |
        //     alignleft aligncenter alignright alignjustify | bullist numlist | outdent indent blockquote |
        //     undo redo | link unlink image code | removeformat`,
        // toolbar: 'bold italic underline strikethrough subscript superscript removeformat | fontselect | fontsizeselect | styleselect | forecolor backcolor | table | image |alignleft aligncenter alignright alignjustify | bullist numlist | outdent indent blockquote |undo redo | ',

        // 工具栏1
        toolbar1:
          "bold italic underline strikethrough subscript superscript removeformat | fontselect | fontsizeselect | styleselect | forecolor backcolor | ",
        // 工具栏2
        toolbar2:
          " table | image | alignleft aligncenter alignright alignjustify | bullist numlist | outdent indent blockquote |undo redo",
        contextmenu: false, // 禁用富文本的右键菜单，使用浏览器自带的右键菜单
        height: 500,
        ...this.option
      },
      unsteady: true,
      dialogVisible: false,
      checkList: ["0", "1", "2"],
      unsteadyForm: {
        unsteadyName: "",
        unsteadyTime: [],
        reportTime: [],
        unsteadyNum: "",
        pattern: "",
        unsteadyAddress: [],
        unsteadyAddressDetail: "",
        unsteadyDetail: "",
        unsteadyNames: "",
        unsteadyPhone: "",
        unsteadyEmail: ""
      },
      checkForm: {
        surname: "",
        phone: "",
        email: "",
        sex: "",
        age: "",
        position: "",
        record: "",
        school: "",
        major: ""
      },
      cityList: [],
      content: null,
      props: {
        value: "code",
        label: "tag",
        children: "children"
      },
      rules: {
        unsteadyName: [
          { required: true, message: "请输入活动名称", trigger: "blur" },
          { min: 5, max: 20, message: "长度在 5 到 20 个字符", trigger: "blur" }
        ],
        unsteadyTime: [
          { required: true, message: "请选择活动时间", trigger: "blur" }
        ],
        reportTime: [
          { required: true, message: "请选择活动时间", trigger: "blur" }
        ],
        unsteadyNum: [
          { required: true, message: "请输入报名人数", trigger: "blur" },
          { min: 0, max: 1000, message: "最高限制1000人", trigger: "blur" }
        ],
        pattern: [
          { required: true, message: "请选择活动方式", trigger: "blur" }
        ],
        unsteadyAddress: [
          { required: true, message: "请选择活动地址", trigger: "blur" }
        ],
        unsteadyDetail: [
          { required: true, message: "请输入活动详情", trigger: "blur" }
        ],
        unsteadyNames: [
          { required: true, message: "请输入联系人姓名", trigger: "blur" }
        ],
        unsteadyPhone: [
          { required: true, message: "请输入联系人手机号", trigger: "blur" },
          {
            pattern: /^[1][356789][0-9]{9}$/,
            message: "请输入正确的手机号",
            trigger: ["change", "blur"]
          }
        ],
        unsteadyEmail: [
          { required: true, message: "请输入联系人邮箱", trigger: "blur" },
          {
            pattern: /^([a-zA-Z0-9_.-])+@(([a-zA-Z0-9-])+\.)+(com|cn|net|com.cn|com.tw|com.hk)$/,
            message: "邮箱格式错误",
            trigger: ["change", "blur"]
          }
        ]
      },
      formAttributeBodies: [
        {
          chineseName: "",
          groupId: 0,
          englishName: "",
          isNumeric: true,
          unit: null
        }
      ]
    };
  },
  mounted() {
    tinymce.init(this.init);
  },
  methods: {
    //图片上传
    previews() {
     this.dialogDrag = true
    },
    //上传附件简历
    handleVideoSuccess(res, file) {
      console.log(file.response.data.fileAccessVo.accessUrl);
      this.dialogDrag = false;
      tinyMCE.editors["tinymce"].insertContent(
        '<img src = "' + file.response.data.fileAccessVo.accessUrl + '">'
      );
    },
    //图片上传
    dealWithUploadLicense(res, file) {
      this.imageUrl = URL.createObjectURL(file.raw);
      this.file = res.data;
      this.$notify({
        title: "成功",
        message: "图片上传成功",
        type: "success"
      });
    },
    //发布
    submitForm(formName) {
      this.form();
      let activityStartTime = this.unsteadyForm.unsteadyTime[0].getTime();
      let activityEndTime = this.unsteadyForm.unsteadyTime[1].getTime();
      let registrationStartTime = this.unsteadyForm.reportTime[0].getTime();
      let registrationEndTime = this.unsteadyForm.reportTime[1].getTime();
      let CodeTo = CodeToTag(
        [
          this.unsteadyForm.unsteadyAddress[0],
          this.unsteadyForm.unsteadyAddress[1],
          this.unsteadyForm.unsteadyAddress[2]
        ],
        this.cityList
      );
      let params = {
        activityAddressId: null,
        activityContent: this.unsteadyForm.unsteadyDetail,
        activityEndTime: activityEndTime,
        activityFormBody: {
          formAttributeBodies: this.formAttributeBodies,
          groupName: null
        },
        addressBody: {
          city: CodeTo[1],
          cityCode: this.unsteadyForm.unsteadyAddress[1],
          detail: this.unsteadyForm.unsteadyAddressDetail
            ? this.unsteadyForm.unsteadyAddressDetail
            : null,
          district: CodeTo[2],
          districtCode: this.unsteadyForm.unsteadyAddress[2],
          latitude: null,
          longitude: null,
          province: CodeTo[0],
          provinceCode: this.unsteadyForm.unsteadyAddress[0]
        },
        activityMode: this.unsteadyForm.pattern,
        activityName: this.unsteadyForm.unsteadyName,
        activityPoster: this.file,
        activityPriority: null,
        activityStartTime: activityStartTime,
        contactEmail: this.unsteadyForm.unsteadyEmail,
        contactName: this.unsteadyForm.unsteadyNames,
        contactPhone: this.unsteadyForm.unsteadyPhone,
        registrationEndTime: registrationEndTime,
        registrationNum: this.unsteadyForm.unsteadyNum,
        registrationStartTime: registrationStartTime
      };
      this.$http
        .post("/business-core/activity", params)
        .then(res => {
          if (res.data.code == "200") {
            this.$notify({
              title: "成功",
              message: "活动发布成功",
              type: "success"
            });
            this.$router.push({
              path: "/unsteady/supervise"
            });
          } else {
          }
        })
        .catch(error => {});
    },
    //预览表单
    preview() {
      this.form();
      this.dialogVisible = true;
    },
    //form
    form() {
      this.checkList.forEach((item, index, array) => {
        //执行代码
        console.log(item);
        switch (item) {
          case "0":
            this.formAttributeBodies[index] = {
              chineseName: "姓名",
              groupId: 0,
              englishName: "surname",
              isNumeric: false,
              unit: null,
              index: 0
            };
            break;
          case "1":
            this.formAttributeBodies[index] = {
              chineseName: "手机",
              groupId: 0,
              englishName: "phone",
              isNumeric: true,
              unit: null,
              index: 1
            };
            break;
          case "2":
            this.formAttributeBodies[index] = {
              chineseName: "邮箱",
              groupId: 0,
              englishName: "email",
              isNumeric: false,
              unit: null,
              index: 2
            };
            break;
          case "3":
            this.formAttributeBodies[index] = {
              chineseName: "性别",
              groupId: 0,
              englishName: "sex",
              isNumeric: false,
              unit: null,
              index: 3
            };
            break;
          case "4":
            this.formAttributeBodies[index] = {
              chineseName: "年龄",
              groupId: 0,
              englishName: "age",
              isNumeric: true,
              unit: null,
              index: 4
            };
            break;
          case "5":
            this.formAttributeBodies[index] = {
              chineseName: "职位",
              groupId: 0,
              englishName: "position",
              isNumeric: false,
              unit: null,
              index: 5
            };
            break;
          case "6":
            this.formAttributeBodies[index] = {
              chineseName: "学历",
              groupId: 0,
              englishName: "record",
              isNumeric: false,
              unit: null,
              index: 6
            };
            break;
          case "7":
            this.formAttributeBodies[index] = {
              chineseName: "学校",
              groupId: 0,
              englishName: "school",
              isNumeric: false,
              unit: null,
              index: 7
            };
            break;
          case "8":
            this.formAttributeBodies[index] = {
              chineseName: "专业",
              groupId: 0,
              englishName: "major",
              isNumeric: false,
              unit: null,
              index: 8
            };
            break;
        }
        console.log(this.formAttributeBodies);
      });
    },
    //上一步
    back() {
      this.unsteady = true;
    },
    //下一步
    next(formName) {
      this.$refs[formName].validate(valid => {
        if (valid) {
          this.unsteady = false;
        } else {
          console.log("error submit!!");
          return false;
        }
      });
    },
    // 插入图片的方法
    handleImageAdded(blobInfo, success, failure) {},
    //发布须知
    attention() {
      this.$router.push({
        path: "/unsteady/attention"
      });
    }
  },
  computed: {
    uploadUrl() {
      return "/api/v2/file-service/files/upload";
    }
  },
  created() {
    this.cityList = city.data;
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
      color: #327CF3;
      font-size: 16px;
      margin: 14px 0 0 20px;
    }

    .title-content {
      font-family: PingFangSC-Medium;
      color: #737373;
      font-size: 14px;
      cursor: pointer;
      margin: 16px 20px 0 0;
    }
  }

  .content {
    width: 963px;
    height: auto;
    background: #FFFFFF;
    border: 1px solid rgba(246, 246, 246, 1);
    box-shadow: 0px 2px 12px 0px rgba(0, 0, 0, 0.06);

    .el-input.is-disabled .el-input__inner {
      background-color: #f5f7fa;
      border-color: #e4e7ed;
      color: #c0c4cc;
      cursor: pointer;
    }

    .demo-ruleForm {
      margin: 20px 0 0 0;

      .unsteadyName {
        width: 595px;
      }

      .upload-demo {
        display: flex;
        flex-direction: row;

        .avatar {
          width: 100%;
          height: auto;
        }

        .el-upload__tip {
          width: 156px;
          height: auto;
          font-family: PingFangSC-Regular;
          color: #848484;
          font-size: 12px;
          margin: 7px 0 0 25px;
        }

        .el-upload-list {
          margin: 0;
          padding: 0;
          list-style: none;
          display: none;
        }
      }

      .unsteadyNum {
        width: 156px;
      }

      .pattern {
        display: flex;
        flex-direction: column;

        span {
          font-family: PingFangSC-Regular;
          color: #AAAAAA 100%;
          font-size: 14px;
        }
      }

      .unsteadyAddress {
      }
    }

    .demo-ruleForms {
      display: flex;
      flex-direction: row;
      margin: 20px 0 0 0;
    }

    .checkedInput {
      width: 202px;
      height: 40px;
      padding: 20px 0 0 20px;
    }

    .line {
      width: 923px;
      border: 1px solid #f1f3f4;
      margin: 0 20px 20px 20px;
    }

    .footer {
      text-align: center;
      font-family: PingFangSC-Regular;
      color: #666666;
      font-size: 16px;
      margin: 0 0 200px 0;
    }
  }
}
</style>