<template>
  <div style="margin:96px 0 0 0;">
    <el-dialog title width="30%" :visible.sync="dialogetx" style="border-radius:5px;">
      <div>
        <pdf ref="pdf" :src="url"></pdf>
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
    <el-dialog
      title
      :visible.sync="DialogVisibleOffer"
      width="23%"
      center
      :show-close="false"
      style="margin:100px 0 0 0"
    >
      <div
        style="text-align:center;font-family: PingFangSC-Medium;color: #111111;font-size:20px"
      >确定发送Offer ？</div>
      <div style="margin:50px 0 0 21%">
        <el-button @click="DialogVisibleOffer = false">取 消</el-button>
        <el-button style="margin:0 0 0 40px" type="primary" @click="transmitOffer()">确 定</el-button>
      </div>
      <div style="height:35px"></div>
    </el-dialog>

    <div class="personal-title">
      <el-dialog title="面试邀请预览" :visible.sync="dialogVisibledit" width="32%" center>
        <div style="margin:0 0 0 20px">
          <div class="title">{{this.ruleForm.user}} 您好！</div>
          <div class="title">恭喜您获得面试邀请，现邀请您参加面试，面试信息如下：</div>
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
    <div class="personal-offer">
      <el-dialog title="发送Offer" :visible.sync="dialogVisibleoffer" width="50%">
        <div>
          <editor v-model="detailOffer" id="tinymces" :init="init"></editor>
        </div>
        <div class="annex">
          <div>上传Offer附件(选填<10mb)：</div>
          <div style="margin:-5px 0 0 0">
            <el-upload
              class="upload-demo"
              :action="uploadUrl"
              :data="uploadDatas"
              :headers="myHeaders"
              :show-file-list="false"
              :on-success="handleAvatarSuccess"
            >
              <div
                v-if="this.file.fileAccessVo"
                style="margin:5px 0 0 0"
              >{{this.file.fileAccessVo.fileName}}</div>
              <el-button v-else size="small" plain type="primary">添加文件</el-button>
            </el-upload>
          </div>
        </div>
        <span slot="footer" class="dialog-footer">
          <el-button @click="previewOffer">预 览</el-button>
          <el-button type="primary" @click="keepReqOffer()">发 送</el-button>
        </span>
      </el-dialog>
      <el-dialog title="预览Offer" :visible.sync="dialogVisiblepreviewOffer" width="50%">
        <div style="width:100%;border: 1px solid rgba(210,210,210,1);border-radius:5px">
          <div style="margin:0 20px" v-html="this.detailOffer"></div>
        </div>
        <span slot="footer" class="dialog-footer">
          <el-button @click="dialogVisiblepreviewOffer = false">返 回</el-button>
          <el-button type="primary" @click="keepReqOffer()">发 送</el-button>
        </span>
      </el-dialog>
    </div>
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
    <div class="resume-nav">
      <div style="width:150px;">{{positionDetail.positionName.substring(0,7)}}</div>
      <div style="margin:0 0 0 20px;width:100%;">{{positionDetail.description.substring(0,60)}}</div>
    </div>

    <div class="resume-seconds">
      <div class="demo-form">
        <el-form :inline="true" :model="formInline" class="demo-form-inline">
          <el-form-item label="求职状态">
            <el-select style="width:200px" v-model="formInline.state" placeholder="请选择求职状态">
              <el-option-group v-for="group in options" :key="group.label" :label="group.label">
                <el-option
                  v-for="item in group.options"
                  :key="item.value"
                  :label="item.label"
                  :value="item.value"
                ></el-option>
              </el-option-group>
            </el-select>
          </el-form-item>
          <el-form-item v-if="showForm">
            <span style="color: #FF7152;" @click="checkForm">
              收起
              <i class="el-icon-caret-top"></i>
            </span>
          </el-form-item>
          <el-form-item v-else>
            <span style="color: #FF7152;" @click="checkForm">
              展开
              <i class="el-icon-caret-bottom"></i>
            </span>
          </el-form-item>
        </el-form>
      </div>
      <el-form
        :inline="true"
        :model="form"
        label-width="90px"
        class="demo-form-inline"
        v-if="showForm"
      >
        <el-form-item label="地区">
          <el-cascader
            style="width:200px"
            v-model="form.city"
            :props="props"
            :options="city"
            @change="handleChange"
          ></el-cascader>
        </el-form-item>
        <el-form-item label="投递时间">
          <el-date-picker
            v-model="form.time"
            type="daterange"
            range-separator="至"
            start-placeholder="开始日期"
            end-placeholder="结束日期"
          ></el-date-picker>
        </el-form-item>
        <el-form-item label="简历完整度">
          <el-select style="width:200px" v-model="form.completedPercent" placeholder="请选择简历完整度">
            <el-option
              v-for="item in optionsResume"
              :key="item.value"
              :label="item.label"
              :value="item.value"
            ></el-option>
          </el-select>
        </el-form-item>
        <el-form-item style="display: flex;justify-content: flex-end;">
          <el-button @click="resetForm">重置</el-button>
          <el-button @click="onSubmit" style="background:#FF7152;color:#ffffff">查询</el-button>
        </el-form-item>
      </el-form>
      <div class="tab-operation">
        <span @click="uploadFile" style="color:#FF7152;font-size:12px">批量下载简历</span>
      </div>
      <!-- <div class="tab-operations" v-if="ms">
        <ul
          class="el-dropdown-menu el-popper"
          id="dropdown-menu-6024"
          style="position: absolute; top: 40px; left: 160px; transform-origin: center top; z-index: 2011;width:110px;"
          x-placement="bottom-end"
        >
          <li
            tabindex="-1"
            @click="interviewstatus(1)"
            :class="this.interviewStates== null ? 'dropdown-buttons' : 'el-dropdown-menu__item'"
          >全部</li>
          <li
            tabindex="-1"
            @click="interviewstatus(2)"
            :class="this.interviewStates== 'TO_BE_ACCEPTED' ? 'dropdown-buttons' : 'el-dropdown-menu__item'"
          >待接受</li>
          <li
            tabindex="-1"
            @click="interviewstatus(3)"
            :class="this.interviewStates== 'REFUSED' ? 'dropdown-buttons' : 'el-dropdown-menu__item'"
          >已拒绝</li>
          <li
            tabindex="-1"
            @click="interviewstatus(4)"
            :class="this.interviewStates== 'TO_BE_INTERVIEWED' ? 'dropdown-buttons' : 'el-dropdown-menu__item'"
          >待面试</li>
          <li
            tabindex="-1"
            @click="interviewstatus(5)"
            :class="this.interviewStates== 'TO_CANCEL_THE_INTERVIEW' ? 'dropdown-buttons' : 'el-dropdown-menu__item'"
          >取消面试</li>
          <li
            tabindex="-1"
            @click="interviewstatus(6)"
            :class="this.interviewStates== 'COMPLETED' ? 'dropdown-buttons' : 'el-dropdown-menu__item'"
          >已完成</li>
          <li
            tabindex="-1"
            @click="interviewstatus(7)"
            :class="this.interviewStates== 'HAS_BEEN_EFFECTIVE' ? 'dropdown-buttons' : 'el-dropdown-menu__item'"
          >已失效</li>
          <div x-arrow class="popper__arrow" style="left: 59px;"></div>
        </ul>
      </div>-->
      <el-tabs
        v-model="activeName"
        type="card"
        v-if="this.action === false"
        @tab-click="handleClicks"
        style="width:98%;margin:0 auto"
      >
        <el-tab-pane label="未处理" name="first">
          <el-table :data="tableData" style="width: 100%" @selection-change="handleSelectionChange">
            <el-table-column type="selection" width="55"></el-table-column>
            <el-table-column prop="fullName" label="姓名" show-overflow-tooltip></el-table-column>
            <el-table-column prop="workAge" label="工作年限" show-overflow-tooltip></el-table-column>
            <el-table-column prop="jobSearchStatus" label="求职状态" show-overflow-tooltip></el-table-column>
            <el-table-column prop="targetCity" label="所在地区" show-overflow-tooltip></el-table-column>
            <el-table-column label="简历完整度" show-overflow-tooltip>
              <template slot-scope="scope">
                <span>{{scope.row.completedPercent}}%</span>
              </template>
            </el-table-column>
            <el-table-column label="投递时间" show-overflow-tooltip>
              <template slot-scope="scope">
                <span>{{scope.row.submittedTime|formatDateOne}}</span>
              </template>
            </el-table-column>
            <el-table-column prop="name" label="操作" width="220">
              <template slot-scope="scope">
                <el-button
                  style="color:#FF7152"
                  v-if="scope.row.isResumeAttached"
                  @click="fileUrl(scope.row)"
                  type="text"
                  size="small"
                >查看附件</el-button>
                <el-button
                  style="color:#FF7152"
                  v-else
                  @click="examing(scope.row)"
                  type="text"
                  size="small"
                >查看在线</el-button>
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
        </el-tab-pane>
        <el-tab-pane label="录用" name="second">
          <el-table :data="tableData" style="width: 100%" @selection-change="handleSelectionChange">
            <el-table-column type="selection" width="55"></el-table-column>
            <el-table-column prop="fullName" label="姓名" show-overflow-tooltip></el-table-column>
            <el-table-column prop="workAge" label="工作年限" show-overflow-tooltip></el-table-column>
            <el-table-column prop="jobSearchStatus" label="求职状态" show-overflow-tooltip></el-table-column>
            <el-table-column prop="targetCity" label="所在地区" show-overflow-tooltip></el-table-column>
            <el-table-column label="简历完整度" show-overflow-tooltip>
              <template slot-scope="scope">
                <span>{{scope.row.completedPercent}}%</span>
              </template>
            </el-table-column>
            <el-table-column label="投递时间" show-overflow-tooltip>
              <template slot-scope="scope">
                <span>{{scope.row.submittedTime|formatDateOne}}</span>
              </template>
            </el-table-column>
            <el-table-column prop="address" label="操作时间" show-overflow-tooltip>
              <template slot-scope="scope">
                <span>{{scope.row.operatorTime|formatDateOne}}</span>
              </template>
            </el-table-column>
            <el-table-column prop="operatorName" label="操作员" show-overflow-tooltip></el-table-column>
            <el-table-column prop="name" label="操作">
              <template slot-scope="scope">
                <el-button
                  style="color:#FF7152"
                  v-if="scope.row.isResumeAttached"
                  @click="fileUrl(scope.row)"
                  type="text"
                  size="small"
                >查看附件</el-button>
                <el-button
                  style="color:#FF7152"
                  v-else
                  @click="examing(scope.row)"
                  type="text"
                  size="small"
                >查看在线</el-button>
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
        </el-tab-pane>
        <el-tab-pane label="不合格" name="third">
          <el-table :data="tableData" style="width: 100%" @selection-change="handleSelectionChange">
            <el-table-column type="selection" width="55"></el-table-column>
            <el-table-column prop="fullName" label="姓名" show-overflow-tooltip></el-table-column>
            <el-table-column prop="workAge" label="工作年限" show-overflow-tooltip></el-table-column>
            <el-table-column prop="jobSearchStatus" label="求职状态" show-overflow-tooltip></el-table-column>
            <el-table-column prop="targetCity" label="所在地区" show-overflow-tooltip></el-table-column>
            <el-table-column label="简历完整度" show-overflow-tooltip>
              <template slot-scope="scope">
                <span>{{scope.row.completedPercent}}%</span>
              </template>
            </el-table-column>
            <el-table-column label="投递时间" show-overflow-tooltip>
              <template slot-scope="scope">
                <span>{{scope.row.submittedTime|formatDateOne}}</span>
              </template>
            </el-table-column>
            <el-table-column prop="address" label="操作时间" show-overflow-tooltip>
              <template slot-scope="scope">
                <span>{{scope.row.operatorTime|formatDateOne}}</span>
              </template>
            </el-table-column>
            <el-table-column prop="operatorName" label="操作员" show-overflow-tooltip></el-table-column>
            <el-table-column prop="name" label="操作">
              <template slot-scope="scope">
                <el-button
                  style="color:#FF7152"
                  v-if="scope.row.isResumeAttached"
                  @click="fileUrl(scope.row)"
                  type="text"
                  size="small"
                >查看附件</el-button>
                <el-button
                  style="color:#FF7152"
                  v-else
                  @click="examing(scope.row)"
                  type="text"
                  size="small"
                >查看在线</el-button>
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
        </el-tab-pane>
        <el-tab-pane label="已入职" name="fourth">
          <el-table :data="tableData" style="width: 100%" @selection-change="handleSelectionChange">
            <el-table-column type="selection" width="55"></el-table-column>
            <el-table-column prop="fullName" label="姓名" show-overflow-tooltip></el-table-column>
            <el-table-column prop="workAge" label="工作年限" show-overflow-tooltip></el-table-column>
            <el-table-column prop="jobSearchStatus" label="求职状态" show-overflow-tooltip></el-table-column>
            <el-table-column prop="targetCity" label="所在地区" show-overflow-tooltip></el-table-column>
            <el-table-column label="简历完整度" show-overflow-tooltip>
              <template slot-scope="scope">
                <span>{{scope.row.completedPercent}}%</span>
              </template>
            </el-table-column>
            <el-table-column label="投递时间" show-overflow-tooltip>
              <template slot-scope="scope">
                <span>{{scope.row.submittedTime|formatDateOne}}</span>
              </template>
            </el-table-column>
            <el-table-column prop="name" label="操作">
              <template slot-scope="scope">
                <el-button
                  style="color:#FF7152"
                  v-if="scope.row.isResumeAttached"
                  @click="fileUrl(scope.row)"
                  type="text"
                  size="small"
                >查看附件</el-button>
                <el-button
                  style="color:#FF7152"
                  v-else
                  @click="examing(scope.row)"
                  type="text"
                  size="small"
                >查看在线</el-button>
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
        </el-tab-pane>
      </el-tabs>
      <el-tabs
        v-model="activeName"
        type="card"
        v-else
        @tab-click="handleClick"
        style="width:98%;margin:0 auto"
      >
        <el-tab-pane label="待查看" name="first">
          <el-table :data="tableData" style="width: 100%" @selection-change="handleSelectionChange">
            <el-table-column type="selection" width="55"></el-table-column>
            <el-table-column prop="fullName" label="姓名" show-overflow-tooltip></el-table-column>
            <el-table-column prop="workAge" label="工作年限" show-overflow-tooltip></el-table-column>
            <el-table-column prop="jobSearchStatus" label="求职状态" show-overflow-tooltip></el-table-column>
            <el-table-column prop="targetCity" label="所在地区" show-overflow-tooltip></el-table-column>
            <el-table-column label="简历完整度" show-overflow-tooltip>
              <template slot-scope="scope">
                <span>{{scope.row.completedPercent}}%</span>
              </template>
            </el-table-column>
            <el-table-column label="投递时间" show-overflow-tooltip>
              <template slot-scope="scope">
                <span>{{scope.row.submittedTime|formatDate}}</span>
              </template>
            </el-table-column>
            <el-table-column prop="name" label="操作" width="220">
              <template slot-scope="scope">
                <el-button
                  style="color:#FF7152"
                  v-if="scope.row.isResumeAttached"
                  @click="fileUrl(scope.row)"
                  type="text"
                  size="small"
                >查看附件</el-button>
                <el-button
                  style="color:#FF7152"
                  v-else
                  @click="examing(scope.row)"
                  type="text"
                  size="small"
                >查看在线</el-button>
                <el-button
                  style="color:#FF7152"
                  @click="unfit(scope.row)"
                  type="text"
                  size="small"
                >不合适</el-button>
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
        </el-tab-pane>
        <el-tab-pane label="已查看" name="sixth">
          <el-table :data="tableData" style="width: 100%" @selection-change="handleSelectionChange">
            <el-table-column type="selection" width="55"></el-table-column>
            <el-table-column prop="fullName" label="姓名" show-overflow-tooltip></el-table-column>
            <el-table-column prop="workAge" label="工作年限" show-overflow-tooltip></el-table-column>
            <el-table-column prop="jobSearchStatus" label="求职状态" show-overflow-tooltip></el-table-column>
            <el-table-column prop="targetCity" label="所在地区" show-overflow-tooltip></el-table-column>
            <el-table-column label="投递时间" show-overflow-tooltip>
              <template slot-scope="scope">
                <span>{{scope.row.submittedTime|formatDateOne}}</span>
              </template>
            </el-table-column>
            <el-table-column prop="address" label="查看时间" show-overflow-tooltip>
              <template slot-scope="scope">
                <span>{{scope.row.operatorTime|formatDateOne}}</span>
              </template>
            </el-table-column>
            <el-table-column prop="name" label="操作">
              <template slot-scope="scope">
                <el-button
                  style="color:#FF7152"
                  v-if="scope.row.isResumeAttachedd"
                  @click="fileUrl(scope.row)"
                  type="text"
                  size="small"
                >查看附件</el-button>
                <el-button
                  style="color:#FF7152"
                  v-else
                  @click="examing(scope.row)"
                  type="text"
                  size="small"
                >查看在线</el-button>
                <el-button
                  style="color:#FF7152"
                  @click="info(scope.row)"
                  type="text"
                  size="small"
                >面试</el-button>
                <el-button
                  style="color:#FF7152"
                  @click="unfit(scope.row)"
                  type="text"
                  size="small"
                >不合适</el-button>
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
        </el-tab-pane>
        <el-tab-pane name="second">
          <template slot="label">
            <el-dropdown>
              <span class="el-dropdown-link">面试</span>
              <el-dropdown-menu slot="dropdown">
                <el-dropdown-item @click.native="interviewstatus(1)">全部</el-dropdown-item>
                <el-dropdown-item @click.native="interviewstatus(2)">待接受</el-dropdown-item>
                <el-dropdown-item @click.native="interviewstatus(3)">已拒绝</el-dropdown-item>
                <el-dropdown-item @click.native="interviewstatus(4)">待面试</el-dropdown-item>
                <el-dropdown-item @click.native="interviewstatus(5)">取消面试</el-dropdown-item>
                <el-dropdown-item @click.native="interviewstatus(6)">已完成</el-dropdown-item>
                <el-dropdown-item @click.native="interviewstatus(7)">已失效</el-dropdown-item>
              </el-dropdown-menu>
            </el-dropdown>
          </template>
          <!-- <span slot="label" @mouseover="selectStyle()">面试</span> -->
          <el-table :data="tableData" style="width: 100%" @selection-change="handleSelectionChange">
            <el-table-column type="selection" width="55"></el-table-column>
            <el-table-column prop="fullName" label="姓名" show-overflow-tooltip></el-table-column>
            <el-table-column prop="workAge" label="工作年限" show-overflow-tooltip></el-table-column>
            <el-table-column prop="jobSearchStatus" label="求职状态" show-overflow-tooltip></el-table-column>
            <el-table-column prop="targetCity" label="所在地区" show-overflow-tooltip></el-table-column>
            <!-- <el-table-column label="简历完整度" show-overflow-tooltip>
              <template slot-scope="scope">
                <span>{{scope.row.completedPercent}}%</span>
              </template>
            </el-table-column>-->
            <el-table-column label="投递时间" show-overflow-tooltip>
              <template slot-scope="scope">
                <span>{{scope.row.submittedTime|formatDateOne}}</span>
              </template>
            </el-table-column>
            <el-table-column label="面试时间" show-overflow-tooltip>
              <template slot-scope="scope">
                <span>{{scope.row.submittedTime|formatDateOne}}</span>
              </template>
            </el-table-column>
            <el-table-column label="面试状态" show-overflow-tooltip>
              <template slot-scope="scope">
                <span>{{scope.row.interviewState|state}}</span>
              </template>
            </el-table-column>
            <!-- <el-table-column prop="address" label="操作时间" show-overflow-tooltip>
              <template slot-scope="scope">
                <span>{{scope.row.operatorTime|formatDateOne}}</span>
              </template>
            </el-table-column>-->
            <el-table-column prop="name" label="操作">
              <template slot-scope="scope">
                <el-button
                  style="color:#FF7152"
                  v-if="scope.row.isResumeAttachedd"
                  @click="fileUrl(scope.row)"
                  type="text"
                  size="small"
                >查看附件</el-button>
                <el-button
                  style="color:#FF7152"
                  v-else
                  @click="examing(scope.row)"
                  type="text"
                  size="small"
                >查看在线</el-button>
                <el-button
                  v-if="scope.row.interviewState === 'TO_CANCEL_THE_INTERVIEW'"
                  style="color:#FF7152"
                  type="text"
                  @click="info(scope.row)"
                  size="small"
                >面试</el-button>
                <el-button
                  v-if="scope.row.interviewState === 'COMPLETED'"
                  style="color:#FF7152"
                  type="text"
                  @click="info(scope.row)"
                  size="small"
                >复试</el-button>
                <el-button
                  v-if="scope.row.interviewState === 'COMPLETED'"
                  style="color:#FF7152"
                  @click="offer(scope.row)"
                  type="text"
                  size="small"
                >录用</el-button>
                <el-button
                  v-if="scope.row.interviewState === 'TO_BE_ACCEPTED'"
                  style="color:#FF7152"
                  @click="notInterview(scope.row)"
                  type="text"
                  size="small"
                >取消面试</el-button>
                <el-button
                  v-if="scope.row.interviewState === 'TO_BE_INTERVIEWED'"
                  style="color:#FF7152"
                  @click="notInterview(scope.row)"
                  type="text"
                  size="small"
                >取消面试</el-button>
                <el-button
                  v-if="scope.row.interviewState === 'TO_BE_INTERVIEWED'"
                  style="color:#FF7152"
                  @click="overInterview(scope.row)"
                  type="text"
                  size="small"
                >完成面试</el-button>
                <el-button
                  v-if="scope.row.interviewState === 'COMPLETED'"
                  style="color:#FF7152"
                  @click="unfit(scope.row)"
                  type="text"
                  size="small"
                >不合适</el-button>
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
        </el-tab-pane>
        <el-tab-pane label="录用" name="third">
          <el-table :data="tableData" style="width: 100%" @selection-change="handleSelectionChange">
            <el-table-column type="selection" width="55"></el-table-column>
            <el-table-column prop="fullName" label="姓名" show-overflow-tooltip></el-table-column>
            <el-table-column prop="workAge" label="工作年限" show-overflow-tooltip></el-table-column>
            <el-table-column prop="jobSearchStatus" label="求职状态" show-overflow-tooltip></el-table-column>
            <el-table-column prop="targetCity" label="所在地区" show-overflow-tooltip></el-table-column>
            <el-table-column label="简历完整度" show-overflow-tooltip>
              <template slot-scope="scope">
                <span>{{scope.row.completedPercent}}%</span>
              </template>
            </el-table-column>
            <el-table-column label="投递时间" show-overflow-tooltip>
              <template slot-scope="scope">
                <span>{{scope.row.submittedTime|formatDateOne}}</span>
              </template>
            </el-table-column>
            <el-table-column prop="address" label="操作时间" show-overflow-tooltip>
              <template slot-scope="scope">
                <span>{{scope.row.operatorTime|formatDateOne}}</span>
              </template>
            </el-table-column>
            <el-table-column prop="operatorName" label="操作员" show-overflow-tooltip></el-table-column>
            <el-table-column prop="name" label="操作">
              <template slot-scope="scope">
                <el-button
                  style="color:#FF7152"
                  v-if="scope.row.isResumeAttached"
                  @click="fileUrl(scope.row)"
                  type="text"
                  size="small"
                >查看附件</el-button>
                <el-button
                  style="color:#FF7152"
                  v-else
                  @click="examing(scope.row)"
                  type="text"
                  size="small"
                >查看在线</el-button>
                <el-button
                  style="color:#FF7152"
                  @click="employed(scope.row)"
                  type="text"
                  size="small"
                >确认入职</el-button>
                <el-button
                  style="color:#FF7152"
                  @click="reqOffer(scope.row)"
                  type="text"
                  size="small"
                >发送offer</el-button>
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
        </el-tab-pane>
        <el-tab-pane label="已入职" name="fifth">
          <el-table :data="tableData" style="width: 100%" @selection-change="handleSelectionChange">
            <el-table-column type="selection" width="55"></el-table-column>
            <el-table-column prop="fullName" label="姓名" show-overflow-tooltip></el-table-column>
            <el-table-column prop="workAge" label="工作年限" show-overflow-tooltip></el-table-column>
            <el-table-column prop="jobSearchStatus" label="求职状态" show-overflow-tooltip></el-table-column>
            <el-table-column prop="targetCity" label="所在地区" show-overflow-tooltip></el-table-column>
            <el-table-column label="简历完整度" show-overflow-tooltip>
              <template slot-scope="scope">
                <span>{{scope.row.completedPercent}}%</span>
              </template>
            </el-table-column>
            <el-table-column label="投递时间" show-overflow-tooltip>
              <template slot-scope="scope">
                <span>{{scope.row.submittedTime|formatDateOne}}</span>
              </template>
            </el-table-column>
            <el-table-column prop="name" label="操作">
              <template slot-scope="scope">
                <el-button
                  style="color:#FF7152"
                  v-if="scope.row.isResumeAttached"
                  @click="fileUrl(scope.row)"
                  type="text"
                  size="small"
                >查看附件</el-button>
                <el-button
                  style="color:#FF7152"
                  v-else
                  @click="examing(scope.row)"
                  type="text"
                  size="small"
                >查看在线</el-button>
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
        </el-tab-pane>
        <el-tab-pane label="不合格" name="fourth">
          <el-table :data="tableData" style="width: 100%" @selection-change="handleSelectionChange">
            <el-table-column type="selection" width="55"></el-table-column>
            <el-table-column prop="fullName" label="姓名" show-overflow-tooltip></el-table-column>
            <el-table-column prop="workAge" label="工作年限" show-overflow-tooltip></el-table-column>
            <el-table-column prop="jobSearchStatus" label="求职状态" show-overflow-tooltip></el-table-column>
            <el-table-column prop="targetCity" label="所在地区" show-overflow-tooltip></el-table-column>
            <el-table-column label="简历完整度" show-overflow-tooltip>
              <template slot-scope="scope">
                <span>{{scope.row.completedPercent}}%</span>
              </template>
            </el-table-column>
            <el-table-column label="投递时间" show-overflow-tooltip>
              <template slot-scope="scope">
                <span>{{scope.row.submittedTime|formatDateOne}}</span>
              </template>
            </el-table-column>
            <el-table-column prop="address" label="操作时间" show-overflow-tooltip>
              <template slot-scope="scope">
                <span>{{scope.row.operatorTime|formatDateOne}}</span>
              </template>
            </el-table-column>
            <el-table-column prop="operatorName" label="操作员" show-overflow-tooltip></el-table-column>
            <el-table-column prop="name" label="操作">
              <template slot-scope="scope">
                <el-button
                  style="color:#FF7152"
                  v-if="scope.row.isResumeAttached"
                  @click="fileUrl(scope.row)"
                  type="text"
                  size="small"
                >查看附件</el-button>
                <el-button
                  style="color:#FF7152"
                  v-else
                  @click="examing(scope.row)"
                  type="text"
                  size="small"
                >查看在线</el-button>
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
        </el-tab-pane>
      </el-tabs>
    </div>
  </div>
</template>

<script>
import Cookies from "js-cookie";
let token = Cookies.get("Btoken");
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
import citys from "../../assets/city.json";
import qs from "qs";
import pdf from "vue-pdf";

export default {
  name: "home",
  components: { Editor, pdf },
  data() {
    return {
      detailOffer: `<p class="MsoNormal" style="mso-add-space: auto; text-align: center; line-height: 150%;" align="center"><strong><span style="font-family: 微软雅黑; line-height: 150%; font-size: 18pt;"><span style="font-family: 微软雅黑;">录</span> <span style="font-family: 微软雅黑;">用</span> <span style="font-family: 微软雅黑;">通</span> <span style="font-family: 微软雅黑;">知</span> <span style="font-family: 微软雅黑;">书</span></span></strong></p>
<p class="MsoNormal" style="mso-add-space: auto; line-height: 150%;"><strong><span style="font-family: 微软雅黑; line-height: 150%; font-size: 14pt;"><span style="font-family: 微软雅黑;">尊敬的</span></span></strong><strong><u><span style="font-family: 微软雅黑; line-height: 150%; font-size: 14pt;">&nbsp;</span></u></strong><strong><u><span style="font-family: 微软雅黑; line-height: 150%; font-size: 14pt;"><span style="font-family: 微软雅黑;">张三</span></span></u></strong><strong><u><span style="font-family: 微软雅黑; line-height: 150%; font-size: 14pt;">&nbsp;</span></u></strong><strong><span style="font-family: 微软雅黑; line-height: 150%; font-size: 14pt;"><span style="font-family: 微软雅黑;">先生：</span></span></strong></p>
<p class="MsoNormal" style="mso-add-space: auto; text-indent: 21.0000pt; line-height: 150%;"><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;"><span style="font-family: 微软雅黑;">我代表</span></span><u><span style="font-family: 微软雅黑; line-height: 150%; font-size: 10.5pt;">XXXX</span></u><u><span style="font-family: 微软雅黑; line-height: 150%; font-size: 10.5pt;"><span style="font-family: 微软雅黑;">有限公司</span></span></u><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;"><span style="font-family: 微软雅黑;">很高兴地通知您，您已经通过了公司的面试考核，公司拟录用您为正式员工并拟与您签订正式劳动合同。欢迎您加入公司</span></span><u><span style="font-family: 微软雅黑; line-height: 150%; font-size: 10.5pt;">&nbsp;&nbsp;<span style="font-family: 微软雅黑;">市场</span> &nbsp;</span></u><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;"><span style="font-family: 微软雅黑;">部</span></span><strong><span style="font-family: 微软雅黑; line-height: 150%; font-size: 10.5pt;">&nbsp;</span></strong><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;"><span style="font-family: 微软雅黑;">，任</span></span><u><span style="font-family: 微软雅黑; line-height: 150%; font-size: 10.5pt;">&nbsp;&nbsp;<span style="font-family: 微软雅黑;">市场经理</span> &nbsp;</span></u><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;"><span style="font-family: 微软雅黑;">职位，目前您的汇报对象是</span></span><u><span style="font-family: 微软雅黑; line-height: 150%; font-size: 10.5pt;">&nbsp;&nbsp;<span style="font-family: 微软雅黑;">总</span></span></u><u><span style="font-family: 微软雅黑; line-height: 150%; font-size: 10.5pt;"><span style="font-family: 微软雅黑;">经理</span></span></u><u><span style="font-family: 微软雅黑; line-height: 150%; font-size: 10.5pt;"><span style="font-family: 微软雅黑;">：</span></span></u><u><span style="font-family: 微软雅黑; line-height: 150%; font-size: 10.5pt;"><span style="font-family: 微软雅黑;">李四</span></span></u><strong><u><span style="font-family: 微软雅黑; line-height: 150%; font-size: 12pt;">&nbsp;</span></u></strong><u><span style="font-family: 微软雅黑; line-height: 150%; font-size: 10.5pt;"><span style="font-family: 微软雅黑;">先生</span> &nbsp;</span></u><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;"><span style="font-family: 微软雅黑;">。您入职后</span></span><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;"><span style="font-family: 微软雅黑;">的</span></span><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;"><span style="font-family: 微软雅黑;">工作职责请与您的直属上级确认。</span></span></p>
<p class="MsoNormal" style="mso-add-space: auto; line-height: 150%;"><strong><span style="font-family: 微软雅黑; line-height: 150%; font-size: 11pt;"><span style="font-family: 微软雅黑;">您入职后</span></span></strong><strong><span style="font-family: 微软雅黑; line-height: 150%; font-size: 11pt;"><span style="font-family: 微软雅黑;">的薪酬待遇</span></span></strong><strong><span style="font-family: 微软雅黑; line-height: 150%; font-size: 11pt;"><span style="font-family: 微软雅黑;">：</span></span></strong></p>
<p class="MsoNormal" style="mso-add-space: auto; line-height: 150%;"><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;">1．您转正后的税前月固定工资是人民币</span><u><span style="font-family: 微软雅黑; line-height: 150%; font-size: 10.5pt;">&nbsp;</span></u><u><span style="font-family: Arial; line-height: 150%; font-size: 10.5pt;">&yen;</span></u><u><span style="font-family: 微软雅黑; line-height: 150%; font-size: 10.5pt;">XXXXX</span></u><u><span style="font-family: 微软雅黑; line-height: 150%; font-size: 10.5pt;">&nbsp;</span></u><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;"><span style="font-family: 微软雅黑;">元，包含</span></span><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;"><span style="font-family: 微软雅黑;">相关补贴，</span></span><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;"><span style="font-family: 微软雅黑;">全年</span></span><u><span style="font-family: 微软雅黑; line-height: 150%; font-size: 10.5pt;">&nbsp;&nbsp;12 &nbsp;</span></u><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;"><span style="font-family: 微软雅黑;">个月薪资，转</span></span><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;"><span style="font-family: 微软雅黑;">正</span></span><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;"><span style="font-family: 微软雅黑;">后</span></span><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;"><span style="font-family: 微软雅黑;">工资</span></span><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;"><span style="font-family: 微软雅黑;">不一</span></span><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;"><span style="font-family: 微软雅黑;">定是</span></span><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;"><span style="font-family: 微软雅黑;">该</span></span><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;"><span style="font-family: 微软雅黑;">薪资，会</span></span><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;"><span style="font-family: 微软雅黑;">根据</span></span><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;"><span style="font-family: 微软雅黑;">试用期表现</span></span><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;"><span style="font-family: 微软雅黑;">和业绩</span></span><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;"><span style="font-family: 微软雅黑;">情况</span></span><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;"><span style="font-family: 微软雅黑;">可能</span></span><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;"><span style="font-family: 微软雅黑;">进行调整</span></span><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;"><span style="font-family: 微软雅黑;">，最终以</span></span><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;"><span style="font-family: 微软雅黑;">实际发放工资为</span></span><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;"><span style="font-family: 微软雅黑;">准。</span></span></p>
<p class="MsoNormal" style="mso-add-space: auto; line-height: 150%;"><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;">2．试用期薪资按80%</span><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;"><span style="font-family: 微软雅黑;">发放，即</span></span><u><span style="font-family: 微软雅黑; line-height: 150%; font-size: 10.5pt;">&nbsp;</span></u><u><span style="font-family: Arial; line-height: 150%; font-size: 10.5pt;">&yen;</span></u><u><span style="font-family: 微软雅黑; line-height: 150%; font-size: 10.5pt;">XXXXX</span></u><u><span style="font-family: 微软雅黑; line-height: 150%; font-size: 10.5pt;">&nbsp;</span></u><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;"><span style="font-family: 微软雅黑;">元整，试用期</span> </span><u><span style="font-family: 微软雅黑; line-height: 150%; font-size: 10.5pt;">&nbsp;</span></u><u><span style="font-family: 微软雅黑; line-height: 150%; font-size: 10.5pt;">X</span></u><u><span style="font-family: 微软雅黑; line-height: 150%; font-size: 10.5pt;">&nbsp;</span></u><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;"><span style="font-family: 微软雅黑;">个月，</span></span><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;"><span style="font-family: 微软雅黑;">全勤</span></span><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;"><span style="font-family: 微软雅黑;">补贴</span></span><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;"><span style="font-family: 微软雅黑;">除外。</span></span></p>
<p class="MsoNormal" style="mso-add-space: auto; line-height: 150%;"><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;">3</span><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;"><span style="font-family: 微软雅黑;">．</span></span><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;"><span style="font-family: 微软雅黑;">培训期包含在试用期内</span></span><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;"><span style="font-family: 微软雅黑;">，</span></span><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;"><span style="font-family: 微软雅黑;">培训期限为</span></span><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;"><span style="font-family: 微软雅黑;">：</span></span><u><span style="font-family: 微软雅黑; line-height: 150%; font-size: 10.5pt;">X</span></u><u><span style="font-family: 微软雅黑; line-height: 150%; font-size: 10.5pt;"><span style="font-family: 微软雅黑;">个工作日</span></span></u><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;"><span style="font-family: 微软雅黑;">，若您通过了培训期，则培训期工资与试用期工资相同；若未能通过培训期，则仅发放基本工资：</span></span><u><span style="font-family: 微软雅黑; line-height: 150%; font-size: 10.5pt;">&nbsp;</span></u><u><span style="font-family: Arial; line-height: 150%; font-size: 10.5pt;">&yen;</span></u><u><span style="font-family: 微软雅黑; line-height: 150%; font-size: 10.5pt;">XX</span></u><u><span style="font-family: 微软雅黑; line-height: 150%; font-size: 10.5pt;"><span style="font-family: 微软雅黑;">元</span>/天 </span></u><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;"><span style="font-family: 微软雅黑;">。</span></span></p>
<p class="MsoNormal" style="mso-add-space: auto; line-height: 150%;"><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;">4</span><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;"><span style="font-family: 微软雅黑;">．</span></span><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;"><span style="font-family: 微软雅黑;">入职</span></span><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;"><span style="font-family: 微软雅黑;">后开始办理社保（五险），公司将从您的月工资中按国家劳动法规定代扣您个人所得税的个人缴纳部分。</span></span></p>
<p class="MsoNormal" style="mso-add-space: auto; line-height: 150%;"><strong><span style="font-family: 微软雅黑; line-height: 150%; font-size: 11pt;"><span style="font-family: 微软雅黑;">您入职后的福利待遇：</span></span></strong></p>
<p class="MsoNormal" style="mso-add-space: auto; line-height: 150%;"><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;">1．社会福利：按照国家及地方政府规定缴纳的养老保险、失业保险、基本医疗保险、工伤保险、生育保险，公司将从您的月工资中代扣个人应缴部分；</span></p>
<p class="MsoNormal" style="mso-add-space: auto; line-height: 150%;"><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;">2．公司福利：带薪休假、</span><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;"><span style="font-family: 微软雅黑;">绩效奖金、年终奖、</span></span><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;"><span style="font-family: 微软雅黑;">年度体检、生日礼物等。</span></span></p>
<p class="MsoNormal" style="mso-add-space: auto; line-height: 150%;"><strong><span style="font-family: 微软雅黑; line-height: 150%; font-size: 11pt;"><span style="font-family: 微软雅黑;">您入职后的</span></span></strong><strong><span style="font-family: 微软雅黑; line-height: 150%; font-size: 11pt;"><span style="font-family: 微软雅黑;">年度薪酬调整</span></span></strong><strong><span style="font-family: 微软雅黑; line-height: 150%; font-size: 11pt;"><span style="font-family: 微软雅黑;">：</span></span></strong></p>
<p class="MsoNormal" style="mso-add-space: auto; line-height: 150%;"><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;"><span style="font-family: 微软雅黑;">公司每年将结合市场薪酬水平、岗位调整、个人绩效</span></span><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;"><span style="font-family: 微软雅黑;">考核</span></span><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;"><span style="font-family: 微软雅黑;">的结果进行薪酬调整</span></span><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;"><span style="font-family: 微软雅黑;">。</span></span></p>
<p class="MsoNormal" style="mso-add-space: auto; line-height: 150%;"><strong><span style="font-family: 微软雅黑; line-height: 150%; font-size: 11pt;"><span style="font-family: 微软雅黑;">本通知书</span></span></strong><strong><span style="font-family: 微软雅黑; line-height: 150%; font-size: 11pt;"><span style="font-family: 微软雅黑;">的</span></span></strong><strong><span style="font-family: 微软雅黑; line-height: 150%; font-size: 11pt;"><span style="font-family: 微软雅黑;">确认及报到</span></span></strong></p>
<p class="MsoNormal" style="mso-add-space: auto; text-indent: 21.0000pt; mso-layout-grid-align: none; line-height: 150%;"><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;"><span style="font-family: 微软雅黑;">请您于</span> </span><u><span style="font-family: 微软雅黑; line-height: 150%; font-size: 10.5pt;">201</span></u><u><span style="font-family: 微软雅黑; line-height: 150%; font-size: 10.5pt;">8</span></u><u><span style="font-family: 微软雅黑; line-height: 150%; font-size: 10.5pt;">&nbsp;<span style="font-family: 微软雅黑;">年</span> </span></u><u><span style="font-family: 微软雅黑; line-height: 150%; font-size: 10.5pt;">XX</span></u><u><span style="font-family: 微软雅黑; line-height: 150%; font-size: 10.5pt;">&nbsp;<span style="font-family: 微软雅黑;">月</span> </span></u><u><span style="font-family: 微软雅黑; line-height: 150%; font-size: 10.5pt;">XX</span></u><u><span style="font-family: 微软雅黑; line-height: 150%; font-size: 10.5pt;"><span style="font-family: 微软雅黑;">日</span></span></u><strong><u><span style="font-family: 微软雅黑; line-height: 150%; font-size: 10.5pt;"><span style="font-family: 微软雅黑;">前</span></span></u></strong><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;"><span style="font-family: 微软雅黑;">回复</span> <span style="font-family: 微软雅黑;">确认接受此录用通知书并于</span></span><u><span style="font-family: 微软雅黑; line-height: 150%; font-size: 10.5pt;">&nbsp;201</span></u><u><span style="font-family: 微软雅黑; line-height: 150%; font-size: 10.5pt;">8</span></u><u><span style="font-family: 微软雅黑; line-height: 150%; font-size: 10.5pt;">&nbsp;<span style="font-family: 微软雅黑;">年</span> </span></u><u><span style="font-family: 微软雅黑; line-height: 150%; font-size: 10.5pt;">XX</span></u><u><span style="font-family: 微软雅黑; line-height: 150%; font-size: 10.5pt;"><span style="font-family: 微软雅黑;">月</span> </span></u><u><span style="font-family: 微软雅黑; line-height: 150%; font-size: 10.5pt;">XX</span></u><u><span style="font-family: 微软雅黑; line-height: 150%; font-size: 10.5pt;"><span style="font-family: 微软雅黑;">日</span></span></u><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;"><span style="font-family: 微软雅黑;">携带已签字确认的录用通知书到公司报到。第一天入职时，您需要携带以下文件：</span> &nbsp;&nbsp;</span><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;">&nbsp;&nbsp;&nbsp;&nbsp;</span><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;">1. &nbsp;离职证明原件（由您工作过的最近一家单位人事部门开具，需加盖人事部门章或者公司公章，</span><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;"><span style="font-family: 微软雅黑;">无法提供，</span></span><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;"><span style="font-family: 微软雅黑;">请</span></span><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;"><span style="font-family: 微软雅黑;">与人事部沟通</span></span><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;"><span style="font-family: 微软雅黑;">）</span></span></p>
<p class="MsoNormal" style="mso-add-space: auto; line-height: 150%;"><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;">2. &nbsp;</span><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;"><span style="font-family: 微软雅黑;">个人简历</span></span><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;"><span style="font-family: 微软雅黑;">（原件）</span></span></p>
<p class="MsoNormal" style="mso-add-space: auto; line-height: 150%;"><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;">3. &nbsp;一寸照片（</span><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;">1</span><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;"><span style="font-family: 微软雅黑;">张）：近期免冠蓝底彩色照片</span></span></p>
<p class="MsoNormal" style="mso-add-space: auto; text-indent: 15.7500pt; mso-char-indent-count: 1.5000; line-height: 150%;"><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;"><span style="font-family: 微软雅黑;">逾期未确认接受此录用通知书或逾期未报到，以及报到时不能提供上述真实有效文件的，本通知书自始不发生法律效力。</span></span></p>
<p class="MsoNormal" style="mso-add-space: auto; line-height: 150%;"><strong><span style="font-family: 微软雅黑; line-height: 150%; font-size: 12pt;">其他说明：</span></strong></p>
<p class="MsoNormal" style="margin-left: 21.2500pt; mso-add-space: auto; text-indent: -21.2500pt; line-height: 150%; mso-list: l0 level1 lfo1;"><!-- [if !supportLists]--><span style="font-family: 微软雅黑; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;">1.&nbsp;</span><!--[endif]--><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;"><span style="font-family: 微软雅黑;">工作时间：公司的工作时间为早晨</span> </span><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;">8</span><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;"><span style="font-family: 微软雅黑;">：</span> </span><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;">3</span><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;">0至下午1</span><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;">7</span><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;">&nbsp;<span style="font-family: 微软雅黑;">：</span></span><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;">3</span><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;">0。</span></p>
<p class="MsoNormal" style="margin-left: 21.2500pt; mso-add-space: auto; line-height: 150%;"><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;"><span style="font-family: 微软雅黑;">中午</span> 12 ：00 至 13 ： 30为午餐及休息时间。</span></p>
<p class="MsoNormal" style="margin-left: 21.2500pt; mso-add-space: auto; line-height: 150%;"><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;"><span style="font-family: 微软雅黑;">第一天入职时间是</span></span><strong><u><span style="font-family: 微软雅黑; line-height: 150%; font-size: 10.5pt;">&nbsp;9 ：00 </span></u></strong><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;"><span style="font-family: 微软雅黑;">。</span></span></p>
<p class="MsoNormal" style="margin-left: 21.2500pt; mso-add-space: auto; text-indent: -21.2500pt; line-height: 150%; mso-list: l0 level1 lfo1;"><!-- [if !supportLists]--><span style="font-family: 微软雅黑; font-size: 10.5pt;">2.&nbsp;</span><!--[endif]--><span style="font-family: 微软雅黑; line-height: 150%; font-size: 10.5pt;"><span style="font-family: 微软雅黑;">办公地点：</span></span><u><span style="font-family: 微软雅黑; line-height: 150%; font-size: 10.5pt;">&nbsp;</span></u><u><span style="font-family: 微软雅黑; line-height: 150%; font-size: 10.5pt;">XX省</span></u><u><span style="font-family: 微软雅黑; line-height: 150%; font-size: 10.5pt;"><span style="font-family: 微软雅黑;">，</span></span></u><u><span style="font-family: 微软雅黑; line-height: 150%; font-size: 10.5pt;">XX市XX</span></u><u><span style="font-family: 微软雅黑; line-height: 150%; font-size: 10.5pt;"><span style="font-family: 微软雅黑;">区，</span></span></u><u><span style="font-family: 微软雅黑; line-height: 150%; font-size: 10.5pt;">XX路XX号XX</span></u><u><span style="font-family: 微软雅黑; line-height: 150%; font-size: 10.5pt;"><span style="font-family: 微软雅黑;">室。</span></span></u></p>
<p class="MsoNormal" style="margin-left: 21.2500pt; mso-add-space: auto; text-indent: -21.2500pt; line-height: 150%; mso-list: l0 level1 lfo1;"><!-- [if !supportLists]--><span style="font-family: 微软雅黑; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;">3.&nbsp;</span><!--[endif]--><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;"><span style="font-family: 微软雅黑;">如您接受本聘书，请</span> <span style="font-family: 微软雅黑;">回复本邮件，以方便公司尽快为您做入职准备。</span></span></p>
<p class="MsoNormal" style="mso-add-space: auto; line-height: 150%;"><strong><span style="font-family: 微软雅黑; line-height: 150%; font-size: 12pt;">特别提示：</span></strong></p>
<p class="MsoNormal" style="mso-add-space: auto; text-indent: 21.0000pt; line-height: 150%;"><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;"><span style="font-family: 微软雅黑;">本通知书的生效是以您向我公司提供的信息全面、真实为前提。您对本通知书的确认并不表示双方劳动关系的建立，只有在您按照本通知书的要求报道、提供的上述材料真实有效的前提下，才具备签订劳动合同的条件，双方劳动关系的建立以及具体的权利义务最终将以我公司与您签订的书面劳动合同为准。如果您向我公司提供的学历、工作经历等信息不真实，或向我公司隐瞒了以前的不良记录（包括但不限于信用记录、违法记录等），或未向我公司披露与其他雇主尚未解除的劳动关系，或对前雇主仍然负有竞业限制等义务，本通知书自始不发生法律效力且公司有权立即解除与您的劳动合同。</span></span></p>
<p class="MsoNormal" style="mso-add-space: auto; text-indent: 21.0000pt; line-height: 150%;"><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;"><span style="font-family: 微软雅黑;">我们真诚</span></span><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;"><span style="font-family: 微软雅黑;">欢迎您的</span></span><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;"><span style="font-family: 微软雅黑;">加入，我们期待与</span></span><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;"><span style="font-family: 微软雅黑;">您</span></span><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;"><span style="font-family: 微软雅黑;">共同成长</span></span><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;"><span style="font-family: 微软雅黑;">。</span></span></p>
<p class="MsoNormal" style="mso-add-space: auto; text-indent: 21.0000pt; line-height: 150%;"><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;"><span style="font-family: 微软雅黑;">若有任何疑问，请随时来电或发送邮件提出。</span></span></p>
<p class="MsoNormal" style="mso-add-space: auto; text-indent: 21.0000pt; line-height: 150%;"><span style="font-family: 微软雅黑; line-height: 150%; font-size: 10.5pt;">&nbsp;</span></p>
<p class="MsoNormal" style="mso-add-space: auto; text-indent: 21.0000pt; line-height: 150%;"><span style="font-family: 微软雅黑; line-height: 150%; font-size: 10.5pt;">Tel</span><span style="font-family: 微软雅黑; line-height: 150%; font-size: 10.5pt;"><span style="font-family: 微软雅黑;">：</span> 0</span><span style="font-family: 微软雅黑; line-height: 150%; font-size: 10.5pt;">XX</span><span style="font-family: 微软雅黑; line-height: 150%; font-size: 10.5pt;">-</span><span style="mso-spacerun: 'yes'; font-family: Calibri; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;">&nbsp;</span><span style="mso-spacerun: 'yes'; font-family: 宋体; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;">12345678</span><span style="font-family: 微软雅黑; line-height: 150%; font-size: 10.5pt;"><span style="font-family: 微软雅黑;">（公司固话）</span></span></p>
<p class="MsoNormal" style="mso-add-space: auto; text-indent: 21.0000pt; line-height: 150%;"><span style="font-family: 微软雅黑; line-height: 150%; font-size: 10.5pt;">Mobile： </span><span style="font-family: 微软雅黑; line-height: 150%; font-size: 10.5pt;">12345678910</span><span style="font-family: 微软雅黑; line-height: 150%; font-size: 10.5pt;"><span style="font-family: 微软雅黑;">（</span>H</span><span style="font-family: 微软雅黑; line-height: 150%; font-size: 10.5pt;">R</span><span style="font-family: 微软雅黑; line-height: 150%; font-size: 10.5pt;"><span style="font-family: 微软雅黑;">，</span></span><span style="font-family: 微软雅黑; line-height: 150%; font-size: 10.5pt;">XXX</span><span style="font-family: 微软雅黑; line-height: 150%; font-size: 10.5pt;"><span style="font-family: 微软雅黑;">女士）</span></span></p>
<p class="MsoNormal" style="mso-add-space: auto; text-indent: 21.0000pt; line-height: 150%;"><span style="font-family: 微软雅黑; line-height: 150%; font-size: 10.5pt;">Email</span><span style="font-family: 微软雅黑; line-height: 150%; font-size: 10.5pt;"><span style="font-family: 微软雅黑;">：</span> </span><span style="font-family: 微软雅黑; line-height: 150%; font-size: 10.5pt;">xxxx123</span><span style="font-family: Verdana; line-height: 150%; font-size: 10.5pt; background: #ffffff;">@</span><span style="font-family: 宋体; line-height: 150%; font-size: 10.5pt; background: #ffffff;">qq</span><span style="font-family: Verdana; line-height: 150%; font-size: 10.5pt; background: #ffffff;">.com</span><span style="font-family: 宋体; line-height: 150%; font-size: 10.5pt; background: #ffffff;"><span style="font-family: 宋体;">（</span></span><span style="font-family: 宋体; line-height: 150%; font-size: 10.5pt; background: #ffffff;">H</span><span style="font-family: Verdana; line-height: 150%; font-size: 10.5pt; background: #ffffff;">R</span><span style="font-family: 宋体; line-height: 150%; font-size: 10.5pt; background: #ffffff;"><span style="font-family: 宋体;">企业邮箱</span></span><span style="font-family: 宋体; line-height: 150%; font-size: 10.5pt; background: #ffffff;"><span style="font-family: 宋体;">）</span></span></p>
<p class="MsoNormal" style="mso-add-space: auto; text-indent: 21.0000pt; line-height: 150%;"><span style="font-family: 微软雅黑; line-height: 150%; font-size: 10.5pt;">&nbsp;</span></p>
<p class="MsoNormal" style="mso-add-space: auto; text-indent: 21.0000pt; line-height: 150%;"><span style="font-family: 微软雅黑; line-height: 150%; font-size: 10.5pt;">&nbsp;</span></p>
<p class="MsoNormal" style="mso-add-space: auto; text-align: right; line-height: 150%;" align="right"><span style="font-family: 微软雅黑; line-height: 150%; font-size: 10.5pt;">XXXXXX</span><span style="font-family: 微软雅黑; line-height: 150%; font-size: 10.5pt;"><span style="font-family: 微软雅黑;">有限</span></span><span style="font-family: 微软雅黑; line-height: 150%; font-size: 10.5pt;"><span style="font-family: 微软雅黑;">公司</span></span><span style="font-family: 微软雅黑; line-height: 150%; font-size: 10.5pt;">&nbsp;</span></p>
<p class="MsoNormal" style="mso-add-space: auto; text-align: right; line-height: 150%;" align="right"><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;"><span style="font-family: 微软雅黑;">日期</span></span><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;"><span style="font-family: 微软雅黑;">：</span></span><u><span style="font-family: 微软雅黑; line-height: 150%; font-size: 10.5pt;">&nbsp;20</span></u><u><span style="font-family: 微软雅黑; line-height: 150%; font-size: 10.5pt;">20</span></u><u><span style="font-family: 微软雅黑; line-height: 150%; font-size: 10.5pt;">&nbsp;</span></u><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;"><span style="font-family: 微软雅黑;">年</span></span><u><span style="font-family: 微软雅黑; line-height: 150%; font-size: 10.5pt;">&nbsp;</span></u><u><span style="font-family: 微软雅黑; line-height: 150%; font-size: 10.5pt;">XX</span></u><u><span style="font-family: 微软雅黑; line-height: 150%; font-size: 10.5pt;">&nbsp;</span></u><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;"><span style="font-family: 微软雅黑;">月</span></span><u><span style="font-family: 微软雅黑; line-height: 150%; font-size: 10.5pt;">&nbsp;</span></u><u><span style="font-family: 微软雅黑; line-height: 150%; font-size: 10.5pt;">XX</span></u><u><span style="font-family: 微软雅黑; line-height: 150%; font-size: 10.5pt;">&nbsp;</span></u><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;"><span style="font-family: 微软雅黑;">日</span></span></p>
<p class="MsoNormal" style="mso-add-space: auto; mso-pagination: widow-orphan; line-height: 150%;"><span style="mso-spacerun: 'yes'; font-family: 微软雅黑; line-height: 150%; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;">&nbsp;</span></p>
<p class="MsoNormal"><span style="mso-spacerun: 'yes'; font-family: Calibri; font-size: 10.5000pt; mso-font-kerning: 1.0000pt;">&nbsp;</span></p>`,
      ms: false,
      dialogVisibleoffer: false,
      DialogVisibleOffer: false,
      dialogVisiblepreviewOffer: false,
      myHeaders: { "Auth-Token": token },
      uploadDatas: {
        label: "offer-attachment"
      },
      linktitle: "复制链接",
      textlink: "service@163.com",
      ruleFormQuiz: {
        companyName: "",
        oralTime: "",
        positions: "",
        dalary: "",
        address: "",
        user: "",
        phone: "",
        desc: ""
      },
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
          " table  | alignleft aligncenter alignright alignjustify | bullist numlist | outdent indent blockquote |undo redo",
        contextmenu: false, // 禁用富文本的右键菜单，使用浏览器自带的右键菜单
        height: 500,
        ...this.option
      },
      rulesQuiz: {
        oralTime: [
          { required: true, message: "请选择面试时间", trigger: "blur" }
        ],
        phone: [
          { required: true, message: "请填写面试联系方式", trigger: "change" },
          {
            pattern: /^[1][356789][0-9]{9}$/,
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
      url: "",
      page: {
        total: 0,
        pageSize: 10,
        current: 1,
        pageSizeOpts: [10, 20, 30]
      },
      action: "",
      centerDialogVisible: false,
      centerDialogVisibles: false,
      dialogVisibledit: false,
      positionID: "",
      city: [],
      props: {
        value: "code",
        label: "tag",
        children: "children"
      },
      positionDetail: {},
      params: {},
      formInline: {
        state: ""
      },
      form: {
        city: [],
        time: [],
        completedPercent: ""
      },
      options: [
        {
          label: "离职",
          options: [
            {
              value: "0",
              label: "随时到岗"
            },
            {
              value: "1",
              label: "暂不考虑"
            }
          ]
        },
        {
          label: "在职",
          options: [
            {
              value: "2",
              label: "考虑工作"
            },
            {
              value: "3",
              label: "急寻找工作"
            }
          ]
        }
      ],
      optionsResume: [
        {
          value: "0",
          label: "附件简历"
        },
        {
          value: "1",
          label: "90%以上"
        },
        {
          value: "2",
          label: "80%-90%"
        },
        {
          value: "3",
          label: "60%-70%"
        },
        {
          value: "4",
          label: "50%-60%"
        },
        {
          value: "5",
          label: "50%以下"
        }
      ],
      tableData: [],
      activeName: "first",
      showForm: false,
      startTime: "",
      endTime: "",
      processedState: "TO_PROCESS",
      multipleSelection: [],
      arrResume: [],
      arrPosition: [],
      multipleSelection: [],
      dialogVisible: false,
      dialogetx: false,
      addressList: [],
      transId: "",
      interviewStates: null,
      companyAddress: "",
      file: "",
      offerList: {},
      completedPercentMax: "",
      completedPercentMin: "",
      fileResumeId:''
    };
  },
  mounted() {
    tinymce.init(this.init);
  },
  computed: {
    uploadUrl() {
      return "/api/v3/file-service/files/upload";
    }
  },
  methods: {
    //查看附件
    fileUrl(res) {
      console.log(res);
      this.fileResumeId = res.id
      this.$http
        .get(`/business-core/resumes/${res.id}/file/url`)
        .then(res => {
          if (res.data.code === "200") {
            this.previewResume(res);
          } else {
          }
        })
        .catch(error => {});
    },
    //doc docx预览
    previewResume(res) {
      console.log(res);
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
        console.log(res.data.data.accessUrl);
        this.url = res.data.data.accessUrl;
      }
      
      if (this.processedState === "TO_PROCESS") {
        this.$http
          .put(
            `/business-core/position/${this.positionID}/resumes/${this.fileResumeId}/processing`
          )
          .then(res => {
            let response = res.data.data.list;
            if (res.data.code == "200") {
              this.activeName = 'sixth'
              let pars = {tab:"sixth"}
              this.handleClick(pars)
            } else {
            }
          })
          .catch(error => {});
      }
    },
    //预览
    previewOffer() {
      this.dialogVisiblepreviewOffer = true;
    },
    //图片上传
    handleAvatarSuccess(res, file) {
      this.file = res.data;
      this.$notify({
        title: "消息",
        message: "附件上传成功",
        type: "success"
      });
    },
    //发送offer
    reqOffer(res) {
      this.dialogVisibleoffer = true;
      this.offerList = res;
    },
    //确定发送offer
    transmitOffer() {
      let params = {
        attachment: this.file ? this.file : null,
        content: this.detailOffer,
        positionId: this.offerList.positionId,
        resumeId: this.offerList.id
      };
      this.$http
        .post(`/business-core/offer`, params)
        .then(res => {
          if (res.data.code == "200") {
          } else {
            this.dialogVisibleoffer = false;
            this.DialogVisibleOffer = false;
            this.$notify({
              title: "消息",
              message: "Offer发送成功",
              type: "success"
            });
          }
        })
        .catch(error => {});
    },
    keepReqOffer() {
      this.DialogVisibleOffer = true;
    },
    selectStyle() {
      if (this.processedState === "INTERVIEW") {
        this.ms = true;
      } else {
        return;
      }
    },
    outStyle() {
      this.ms = false;
    },
    //取消面试
    notInterview(list) {
      let params = {
        reason: "对不起，您的简历不符合"
      };

      this.$http
        .put(`/business-core/interview/cancel/${list.interviewId}`, params)
        .then(res => {
          if (res.data.code == "200") {
            this.process();
          } else {
          }
        })
        .catch(error => {});
    },
    //完成面试
    overInterview(list) {
      this.$http
        .put(`/business-core/interview/completed/${list.interviewId}`)
        .then(res => {
          if (res.data.code == "200") {
            this.process();
          } else {
          }
        })
        .catch(error => {});
    },
    interviewstatus(e) {
      this.activeName = "second";
      if (e === 1) {
        this.interviewStates = null;
      } else if (e === 2) {
        this.interviewStates = "TO_BE_ACCEPTED";
      } else if (e === 3) {
        this.interviewStates = "REFUSED";
      } else if (e === 4) {
        this.interviewStates = "TO_BE_INTERVIEWED";
      } else if (e === 5) {
        this.interviewStates = "TO_CANCEL_THE_INTERVIEW";
      } else if (e === 6) {
        this.interviewStates = "COMPLETED";
      } else {
        this.interviewStates = "HAS_BEEN_EFFECTIVE";
      }
      this.process();
    },
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
        .post(`/business-core/position/${this.positionID}/resumes/info`, params)
        .then(res => {
          if (res.data.code === "200") {
            this.centerDialogVisibles = false;
            this.centerDialogVisible = false;
            this.process();
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
    uploadFile() {
      // let resumeList = qs.stringify(
      //   {
      //     resumeIds: this.arrResume,
      //     positionIds: this.arrPosition
      //   },
      //   { arrayFormat: "repeat" }
      // );
      let resumeList = {
        resumeIds: this.arrResume,
        positionIds: this.arrPosition
      };
      this.dialogVisible = true;
      this.$local
        .post("/business-core/resumes/batchPackageDownloadLong", resumeList, {
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
          // iso8859-1的字符转换成中文
          fileName = decodeURI(escape(fileName));
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
          if (error.response.status === 404) {
            this.$notify.info({
              title: "消息",
              message: "页面丢失，请重新加载"
            });
          } else if (error.response.status === 403) {
            this.$notify.info({
              title: "消息",
              message: "登陆超时，请重新登录"
            });
          } else {
            this.$notify.info({
              title: "消息",
              message: error.response.data.message
            });
          }
        });
    },
    //不合格
    unfit(tab) {
      this.$http
        .put(
          `/business-core/position/${this.positionID}/resumes/${tab.id}/unfit`
        )
        .then(res => {
          if (res.data.code == "200") {
            this.process();
          } else {
          }
        })
        .catch(error => {});
    },
    //录用
    offer(tab) {
      this.$http
        .put(
          `/business-core/position/${this.positionID}/resumes/${tab.id}/offer`
        )
        .then(res => {
          if (res.data.code == "200") {
            this.process();
          } else {
          }
        })
        .catch(error => {});
    },
    //通知面试/笔试
    info(tab) {
      this.centerDialogVisibles = true;
      console.log(tab);
      this.ruleForm.companyName = tab.companyName;
      this.ruleForm.positions = tab.positionName;
      this.ruleForm.dalary = tab.salary;
      this.transId = tab.id;
    },
    //确认入职
    employed(tab) {
      this.$http
        .put(
          `/business-core/position/${this.positionID}/resumes/${tab.id}/employed`
        )
        .then(res => {
          let response = res.data.data;
          if (res.data.code == "200") {
            this.process();
          } else {
          }
        })
        .catch(error => {});
    },
    //查看
    examing(tab) {
      console.log(tab);
      if (this.processedState === "TO_PROCESS") {
        this.$http
          .put(
            `/business-core/position/${this.positionID}/resumes/${tab.id}/processing`
          )
          .then(res => {
            let response = res.data.data.list;
            if (res.data.code == "200") {
            } else {
            }
          })
          .catch(error => {});
      }
      var arr = JSON.stringify(tab);
      this.$router.push({
        path: "/resume/talent/Detail",
        query: {
          resumeId: tab.id,
          state: 0,
          resinfo: encodeURIComponent(arr),
          positionId: this.positionID,
          processedState: this.processedState
        }
      });
    },
    //通知面试
    quiz() {
      this.centerDialogVisible = true;
    },
    //重置
    resetForm() {
      this.form = {};
      this.formInline.state = "";
    },
    //查询
    onSubmit() {
      this.process();
    },
    //职位详情
    position() {
      this.$http
        .get(`/business-core/positions/${this.positionID}`)
        .then(res => {
          let response = res.data.data;
          if (res.data.code == "200") {
            this.positionDetail = response;
          } else {
          }
        })
        .catch(error => {});
    },
    //待处理
    process() {
      switch (this.form.completedPercent) {
        case "1":
          this.completedPercentMin = 90;
          this.completedPercentMax = null;
          break;
        case "2":
          this.completedPercentMin = 80;
          this.completedPercentMax = 90;
          break;
        case "3":
          this.completedPercentMin = 70;
          this.completedPercentMax = 80;
          break;
        case "4":
          this.completedPercentMin = 60;
          this.completedPercentMax = 70;
          break;
        case "5":
          this.completedPercentMin = 50;
          this.completedPercentMax = 60;
          break;
        case "6":
          this.completedPercentMin = null;
          this.completedPercentMax = 50;
          break;
        default:
          this.completedPercentMin = null;
          this.completedPercentMax = null;
          break;
      }
      if (this.form.time.length !== 0) {
        this.startTime = this.form.time[0].getTime();
        this.endTime = this.form.time[1].getTime();
      } else {
        this.startTime = null;
        this.endTime = null;
      }
      let params = {
        city: this.form.city[1] ? this.form.city[1] : null,
        completedPercentMax: this.completedPercentMax,
        completedPercentMin: this.completedPercentMin,
        jobSearchStatusCode:
          this.formInline.state === "" ? null : Number(this.formInline.state),
        pageNum: this.page.current,
        pageSize: this.page.pageSize,
        processedState: this.processedState,
        interviewState: this.interviewStates,
        province: this.form.city[0] ? this.form.city[0] : null,
        sortBy: null,
        sortOrder: null,
        submittedTimeEnd: this.endTime,
        submittedTimeStart: this.startTime
      };
      this.$http
        .post(`/business-core/position/${this.positionID}/resumes/list`, params)
        .then(res => {
          let response = res.data.data.list;
          if (res.data.code == "200") {
            this.tableData = response;
            this.page.total = res.data.data.total;
          } else {
          }
        })
        .catch(error => {});
    },
    //tab
    mss() {},
    handleClick(tab) {
      if (tab.paneName === "first") {
        this.ms = false;
        this.processedState = "TO_PROCESS";
      } else if (tab.paneName === "second") {
        this.ms = true;
        this.processedState = "INTERVIEW";
      } else if (tab.paneName === "third") {
        this.ms = false;
        this.processedState = "OFFERED";
      } else if (tab.paneName === "fourth") {
        this.ms = false;
        this.processedState = "UNFIT";
      } else if (tab.paneName === "fifth") {
        this.ms = false;
        this.processedState = "EMPLOYED";
      } else {
        this.ms = false;
        this.processedState = "PROCESSING";
      }
      this.process();
    },
    //tab 下线
    handleClicks(tab) {
      if (tab.paneName === "first") {
        this.processedState = "TO_PROCESS";
      } else if (tab.paneName === "second") {
        this.processedState = "OFFERED";
      } else if (tab.paneName === "third") {
        this.processedState = "UNFIT";
      } else {
        this.processedState = "EMPLOYED";
      }
      this.process();
    },
    checkForm() {
      this.showForm = !this.showForm;
    },
    //未处理分页
    handleSizeChange(val) {
      this.page.pageSize = val;
      this.page.current = 1;
      this.process();
    },
    handleCurrentChange(val) {
      this.page.current = val;
      this.process();
    }
  },

  created() {
    this.city = citys.data;
    this.positionID = this.$route.query.positionId;
    this.action = this.$route.query.action;
    this.position();
    this.process();
    this.address();
  },
  filters: {
    level(level) {
      var a;
      switch (level) {
        case "EMPLOYED":
          a = "已入职";
          break;
        default:
          a = "未入职";
          break;
      }
      return a;
    },
    state(state) {
      var a;
      switch (state) {
        case "TO_BE_ACCEPTED":
          a = "待接受";
          break;
        case "REFUSED":
          a = "已拒绝";
          break;
        case "TO_BE_INTERVIEWED":
          a = "待面试";
          break;
        case "COMPLETED":
          a = "已完成";
          break;
        case "TO_CANCEL_THE_INTERVIEW":
          a = "面试取消";
          break;
        case "HAS_BEEN_EFFECTIVE":
          a = "已失效";
          break;
      }
      return a;
    }
  }
};
</script>


<style lang="stylus">
.dropdown-buttons {
  background: #e9ebf4;
  list-style: none;
  line-height: 36px;
  padding: 0 20px;
  margin: 0;
  font-size: 14px;
  color: #606266;
  cursor: pointer;
  outline: none;
}

.personal-title {
  .title {
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

.personal-offer {
  .el-dialog__title {
    font-size: 20px;
    line-height: 25px;
    margin: 0 0 0 380px;
  }

  .el-icon-close:before {
    content: '\e6db';
    font-size: 20px;
  }

  .el-dialog__footer {
    padding: 0px 20px 30px;
    text-align: center;
    box-sizing: border-box;
  }

  .annex {
    display: flex;
    flex-direction: row;
    justify-content: flex-end;
    font-family: PingFangSC-Medium;
    color: #5A5A5A;
    font-size: 16px;
    margin: 25px 0 0 0;

    .el-upload-list {
      margin: 0;
      padding: 0;
      list-style: none;
      display: none;
    }
  }
}

.resume-nav {
  width: 100%;
  height: 50px;
  background: #FAFAFA;
  display: flex;
  flex-direction: row;
  line-height: 50px;
  margin: -80px 0 0 0;

  div:nth-child(1) {
    margin: 0 0 0 20px;
    font-size: 16px;
    color: #02B9B8;
  }

  div:nth-child(2) {
    margin: 0 160px 0 0;
    color: #373737;
    font-size: 14px;
  }
}

.left-second {
  width: 100%;
  height: 80px;
  background: #ffffff;
  text-align: center;
  border: 1px solid rgba(244, 244, 244, 1);
  box-shadow: 0px 2px 10px 0px rgba(245, 245, 245, 1);

  ul {
    margin: 0;
    list-style: none;
    display: flex;
    justify-content: space-around;
    padding: 0;
  }

  li {
    flex: 1;
    display: flex;
    flex-direction: column;
    margin: 10px 0 0 0;

    span:first-child {
      line-height: 27px;
      color: #666;
      font-size: 14px;
    }

    span:last-child {
      line-height: 33px;
      font-size: 24px !important;
      color: #a3292e;
    }
  }
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

.resume-second {
  width: 100%;
  height: 80px;
  background: #ffffff;
  border: 1px solid rgba(244, 244, 244, 1);
  box-shadow: 0px 2px 10px 0px rgba(245, 245, 245, 1);
  text-align: center;

  ul {
    margin: 0;
    list-style: none;
    display: flex;
    justify-content: space-around;
    padding: 0;
  }

  li {
    flex: 1;
    display: flex;
    flex-direction: column;
    margin: 10px 0 0 0;

    span:first-child {
      line-height: 27px;
      color: #666;
      font-size: 14px;
    }

    span:last-child {
      line-height: 33px;
      font-size: 24px !important;
      color: #a3292e;
    }
  }
}

.resume-seconds {
  width: 100%;
  height: auto;
  background: #ffffff;
  border: 1px solid rgba(244, 244, 244, 1);
  box-shadow: 0px 2px 10px 0px rgba(245, 245, 245, 1);

  .tab-operations {
    position: absolute;
    z-index: 999;
    // margin: 7px 0 0 840px;
    font-family: PingFangSC-Regular;

    .el-dropdown-menu {
      position: absolute;
      top: 0;
      left: 0;
      z-index: 10;
      padding: 10px 0;
      margin: 5px 0;
      background-color: #fff;
      border: 1px solid #ebeef5;
      border-radius: 4px;
      box-shadow: 0 2px 12px 0 rgba(0, 0, 0, 0.1);
    }
  }

  .tab-operation {
    position: absolute;
    z-index: 999;
    margin: 7px 0 0 870px;
    font-family: PingFangSC-Regular;
  }

  .demo-form {
    display: flex;
    flex-direction: row;
  }

  .line {
    margin: 0 0 0 10px;
  }
}

.demo-form-inline {
  margin: 20px 20px 0 20px;
}
</style>
