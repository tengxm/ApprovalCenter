<template>
    <div id="ApprovalDetailPage">
        <!--按钮操作区-->
        <!--我发起的审批-->
        <template v-if="pageType==1 && isShowPresetButton">
            <div class="well widget-body" style="text-align:left;" name="ButtonGroup">
                <div class="widget-buttons buttons-bordered " style="border:none;">
                    <button title="撤销" type="button" class="btn btn-default btn-primary" v-on:click="btnOperation('撤销')" :disabled="isDisabled">
                        <i class="fa fa-reply"></i>撤销
                    </button>
                </div>
                <div class="widget-buttons buttons-bordered " style="border:none;">
                    <button title="催办" type="button" class="btn btn-default" v-on:click="btnOperation('催办')" :disabled="isDisabled">
                        <i class="fa fa-bell"></i>催办
                    </button>
                </div>
            </div>
        </template>
        <!--待我审批的-->
        <template v-else-if="pageType==2 && isShowPresetButton">
            <div class="well widget-body" style="text-align:left;" name="ButtonGroup">
                <div class="widget-buttons buttons-bordered " style="border:none;">
                    <button title="通过" type="button" class="btn btn-default btn-primary" v-on:click="btnOperation('转交')" :disabled="isDisabled">
                        <i class="fa fa-exchange"></i>转交
                    </button>
                </div>
                <template v-for="item in btns">
                    <div class="widget-buttons buttons-bordered" style="border:none;" v-show="isShowButton">
                        <button type="button" class="btn btn-default" @click="btnOperation(item)" :disabled="isDisabled">
                            {{item}}
                        </button>
                    </div>
                </template>
            </div>
        </template>
        <!--我已审批的-->
        <template v-else-if="pageType==3">
        </template>
        <!--无权限查看审批页面-->
        <template v-else-if="pageType==4">
        </template>
        <!--发起审批-->
        <template v-else-if="pageType==0 && isShowPresetButton">
            <div class="well widget-body" style="text-align:left;" name="ButtonGroup">
                <div class="widget-buttons buttons-bordered " style="border:none;">
                    <button  title="返回" type="button" class="btn btn-default btn-primary" v-on:click.stop="back">
                        <i class="fa fa-reply "></i>返回
                    </button>
                </div>
                <div class="widget-buttons buttons-bordered " style="border:none;">
                    <button title="提交审批" type="button" class="btn btn-default" v-on:click="btnSubmit" :disabled="isDisabled">
                        <i class="fa fa-arrow-up"></i>提交审批
                    </button>
                </div>
                <!--<div class="widget-buttons buttons-bordered " style="border:none;">-->
                    <!--<button title="保存" type="button" class="btn btn-default btn-primary" v-on:click="btnSave">-->
                        <!--<i class="fa  fa-save"></i>保存草稿-->
                    <!--</button>-->
                <!--</div>-->
            </div>
        </template>
        <!--审批进度显示区-->
        <template v-if="pageType!=0">
            <template v-if="pageType==4">
            </template>
            <template v-else>
                <ApprovalProgressInfo :ProgressId="procInstance.ID"></ApprovalProgressInfo>
            </template>
        </template>

        <!--表单展示区-->
        <div class="well widget-body" v-show="isShowForm">
            <div class="row">
                <form class="form-horizontal bv-form" id="form" role="form">
                    <div class="col-lg-12 col-sm-12 col-xs-12">
                        <h2 id="BusiType" style="text-align: center;">{{basicFormInfo.BusiTypeName}}</h2>
                    </div>
                    <div class="col-lg-10 col-sm-10 col-xs-12" style="padding-left: 0px">
                        <h5 class="row-title before-color">
                            <i class="fa fa-file-text iconcolor"></i>
                            基本信息
                        </h5>
                    </div>
                    <div id="baseInfo" class="row">
                        <div class=" col-lg-12 col-sm-12 col-xs-12">
                            <div class="col-lg-5 col-sm-5 col-xs-12">
                                <div class="form-group">
                                    <label class="col-sm-4 control-label ">*发起人</label>
                                    <div class="col-sm-8">
                                        <input type="text" class="form-control input-sm" name="ApprovalOriginator" v-model="basicFormInfo.OriginatorName" id="ApprovalOriginator"  disabled/>
                                        <input type="hidden" class="form-control input-sm" name="ApprovalOriginatorID" id="ApprovalOriginatorID" v-model="basicFormInfo.OriginatorID"/>
                                    </div>
                                </div>
                            </div>
                            <div class="col-lg-5 col-sm-5 col-xs-12">
                                <div class="form-group">
                                    <label class="col-sm-4 control-label ">*所在部门</label>
                                    <div class="col-sm-8">
                                        <div class="input-group helper-group">
                                            <input type="hidden" class="form-control input-sm" name="OrganizationId" id="OrganizationId"
                                                   data-bv-notempty="true" data-bv-notempty-message="请选择所在部门"/>
                                            <input type="text" class="form-control input-sm" name="Organization" id="Organization"
                                                   data-bv-notempty="true" data-bv-notempty-message="请选择所在部门" disabled/>
                                            <span class="input-group-btn">
                                                <button class="btn btn-default shiny btn-sm" @click.prevent="OrganizationModal" type="button">
                                                    <i class=" fa fa-ellipsis-h"></i>
                                                </button>
                                            </span>
                                        </div>
                                    </div>
                                </div>
                            </div>
                        </div>
                        <div class=" col-lg-12 col-sm-12 col-xs-12">
                            <template v-if="pageType!=0">
                                <div class="col-lg-5 col-sm-5 col-xs-12">
                                    <div class="form-group">
                                        <label class="col-sm-4 control-label ">*业务编号</label>
                                        <div class="col-sm-8">
                                            <input class="form-control input-sm" name="BusiCode" id="BusiCode" placeholder="请输入业务编号" disabled
                                                   data-bv-notempty="true" data-bv-notempty-message="请输入业务编号" v-model="basicFormInfo.Folio"/>
                                        </div>
                                    </div>
                                </div>
                            </template>
                            <div class="col-lg-5 col-sm-5 col-xs-12">
                                <div class="form-group">
                                    <label class="col-sm-4 control-label ">*业务描述</label>
                                    <div class="col-sm-8">
                                        <input class="form-control input-sm" name="BusiDesc" id="BusiDesc" placeholder="请输入业务描述"
                                               data-bv-notempty="true" data-bv-notempty-message="请输入业务描述" v-model="basicFormInfo.Subject"/>
                                    </div>
                                </div>
                            </div>
                        </div>
                    </div>
                    <slot name="FormContent"></slot>
                </form>
            </div>
        </div>
        <!--无权限展示页面-->
        <div style="text-align: center;margin: 50px auto 0; color:#00a7cb;" v-show="!isShowForm" id="NoPrivilege">
            <h1><i class="fa fa-times-circle"></i></h1>
            <h1 style="font-weight:bold">抱歉！您没有权限访问该审批工作！</h1>
        </div>

        <!--底部按钮区-->
        <!--我发起的审批-->
        <template v-if="pageType==1 && isShowPresetButton">
            <div class="well widget-body" style="text-align:left;" name="ButtonGroup">
                <div class="widget-buttons buttons-bordered " style="border:none;">
                    <button title="撤销" type="button" class="btn btn-default btn-primary" v-on:click="btnOperation('撤销')" :disabled="isDisabled">
                        <i class="fa fa-reply"></i>撤销
                    </button>
                </div>
                <div class="widget-buttons buttons-bordered " style="border:none;">
                    <button title="催办" type="button" class="btn btn-default" v-on:click="btnOperation('催办')" :disabled="isDisabled">
                        <i class="fa fa-bell"></i>催办
                    </button>
                </div>
            </div>
        </template>
        <!--待我审批的-->
        <template v-else-if="pageType==2 && isShowPresetButton">
            <div class="well widget-body" style="text-align:left;" name="ButtonGroup">
                <div class="widget-buttons buttons-bordered " style="border:none;">
                    <button title="转交" type="button" class="btn btn-default btn-primary" v-on:click="btnOperation('转交')" :disabled="isDisabled">
                        <i class="fa fa-exchange"></i>转交
                    </button>
                </div>
                <template v-for="item in btns">
                    <div class="widget-buttons buttons-bordered" style="border:none;" v-show="isShowButton">
                        <button type="button" class="btn btn-default" @click="btnOperation(item)" :disabled="isDisabled">
                            {{item}}
                        </button>
                    </div>
                </template>
            </div>
        </template>
        <!--我已审批的-->
        <template v-else-if="pageType==3">
        </template>
        <!--无权限查看审批页面-->
        <template v-else-if="pageType==4">
        </template>
        <!--发起审批-->
        <template v-else-if="pageType==0 && isShowPresetButton">
            <div class="well widget-body" style="text-align:left;" name="ButtonGroup">
                <div class="widget-buttons buttons-bordered " style="border:none;">
                    <button  title="返回" type="button" class="btn btn-default btn-primary" v-on:click.stop="back">
                        <i class="fa fa-reply "></i>返回
                    </button>
                </div>
                <div class="widget-buttons buttons-bordered " style="border:none;">
                    <button title="提交审批" type="button" class="btn btn-default" v-on:click="btnSubmit" :disabled="isDisabled">
                        <i class="fa fa-arrow-up"></i>提交审批
                    </button>
                </div>
                <!--<div class="widget-buttons buttons-bordered " style="border:none;">-->
                    <!--<button title="保存" type="button" class="btn btn-default" v-on:click="btnSave">-->
                        <!--<i class="fa  fa-save"></i>保存草稿-->
                    <!--</button>-->
                <!--</div>-->
            </div>
        </template>
        <!--审批意见模态框-->
        <div class="modal fade in" role="dialog" aria-labelledby="myModalLabel" aria-hidden="false" id="ApprovalOpinionModal"
             data-backdrop="static">
            <div class="modal-dialog">
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="bootbox-close-button close" data-dismiss="modal" aria-hidden="true" @click="btnClose">×</button>
                        <h6 class="modal-title" id="ModalTitle"></h6>
                    </div>
                    <input type="hidden" id="OperationVal" name="OperationVal" value=""/>
                    <div class="modal-body tabbable form-horizontal bv-form" style="overflow: hidden">
                        <div class="tab-pane in active bv-form">
                            <form role="well widget-body row">
                                <div class="col-lg-12 col-sm-12 col-xs-12" id="TransferInput">
                                    <div class="form-group">
                                        <label>转交给：</label>
                                        <div class="input-group helper-group" style="width: 200px;" id="UserBtnUserHelpDiv">
                                            <input type="hidden" id="hideTransferID" name="hideTransferID" v-model="UserID">
                                            <input type="text" class="form-control input-sm" id="TransferName" name="TransferName" placeholder='请选择转交人' readonly="readonly" style="width:210px" v-model="UserName">
                                            <span class="input-group-btn">
                                                <button class="btn btn-default shiny btn-sm" @click.prevent="UserModal" type="button">
                                                    <i class=" fa fa-ellipsis-h"></i>
                                                </button>
                                            </span>
                                        </div>
                                    </div>
                                </div>
                                <div class="col-lg-12 col-sm-12 col-xs-12">
                                    <div class="form-group" id="InputTitle">
                                        请填写审批意见：
                                    </div>
                                </div>
                                <div class="col-lg-12 col-sm-12 col-xs-12">
                                    <div class="form-group">
                                            <textarea class="form-control" name="Opinion" id="Opinion" rows="4"></textarea>

                                    </div>
                                </div>
                            </form>
                        </div>
                    </div>
                    <div class="modal-footer">
                        <button id="btnSuccess" type="button" class="btn btn-primary" data-dismiss="modal" @click="btnConfirm">确定</button>
                        <button type="button" class="btn" data-dismiss="modal" @click="btnClose">关闭</button>
                    </div>
                </div>
            </div>
        </div>
        <!--所属组织模态框-->
        <div class="modal fade in" tabindex="-1" role="dialog" aria-labelledby="myModalLabel" aria-hidden="false"
             id="OrganizationModal" data-backdrop="static" style="overflow-y: auto">
            <div class="modal-dialog modal-lg">
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="bootbox-close-button close"
                                data-dismiss="modal" aria-hidden="true">×
                        </button>
                        <h6 class="modal-title">所属组织</h6>
                    </div>
                    <div class="modal-body">
                        <div class="form-group">
                            <div class="table-responsive">
                                <table class="table table-striped table-hover table-bordered  max-width-table"
                                       id="organizationdatatable"
                                       style="">
                                    <thead>
                                    <tr role="row">
                                        <th style="width: 60px;" class="text-center">
                                            <label>
                                                序号
                                            </label>
                                        </th>
                                        <th style="width: 200px; " class="text-center Code_col">
                                            <label>
                                                部门名称
                                            </label>
                                        </th>
                                        <th style="width: 120px;" class="text-center Name_col">
                                            <label>
                                                是否默认
                                            </label>
                                        </th>
                                    </tr>
                                    </thead>
                                    <tbody>
                                    <tr v-for="(item,index) in basicFormInfo.OriginatorOrgs">
                                        <td class="text-center" style="width: 60px;">
                                            {{index+1}}
                                        </td>
                                        <td class="orgName" style="width: 200px;" :title="item.Name">
                                            {{item.Name}}
                                            <input type="hidden" name="Name" :value="item.Name"/>
                                        </td>
                                        <td style="display: none;">
                                            <input type="hidden" name="Id" :value="item.ID"/>
                                        </td>
                                        <td class="text-center" style="width: 120px;">
                                            <template v-if="item.IsDefault==0">
                                                <input type="checkbox" class="isDefault" style="position: static;opacity: 1;" @click="IsDefaultFunc" @click.stop="trClick"/>
                                            </template>
                                            <template v-else-if="item.IsDefault==1">
                                                <input type="checkbox" class="isDefault" style="position: static;opacity: 1;" @click="IsDefaultFunc" @click.stop="trClick" checked/>
                                            </template>
                                        </td>
                                    </tr>
                                    <!--<tr>-->
                                        <!--<td class="text-center Name_col" title="" style="width: 60px; height: 35px;"-->
                                            <!--colspan="3">无记录-->
                                        <!--</td>-->
                                    <!--</tr>-->
                                    </tbody>
                                </table>
                            </div>
                        </div>
                    </div>
                    <div class="modal-footer">
                        <button type="button" class="btn btn-primary" data-dismiss="modal" @click="btnOrgConfirm">确定</button>
                        <button type="button" class="btn" data-dismiss="modal">关闭</button>
                    </div>
                </div>
            </div>
        </div>
        <!--用户帮助-->
        <div class="modal fade in" tabindex="-1" role="dialog" aria-labelledby="myModalLabel" aria-hidden="false"
             id="CopyUserModal" data-backdrop="static" style="overflow-y: auto">
            <div class="modal-dialog modal-lg">
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="bootbox-close-button close"
                                data-dismiss="modal" aria-hidden="true">×
                        </button>
                        <h6 class="modal-title">用户帮助</h6>
                    </div>
                    <div class="modal-body" id="DetailDataBody">
                        <div class="table-toolbar">
                            <div class="btn-group">
                                <select class="form-control input-sm" v-model="SearchType">
                                    <option value="">全部</option>
                                    <option value="Account">账号</option>
                                    <option value="RealName">姓名</option>
                                    <option value="Mobile">手机</option>
                                </select>
                            </div>
                            <div class="btn-group">
                                <div class="input-group helper-group" style=" float: left; width: 200px">
                                    <input type="text" class="form-control input-sm" id="copykeywords"
                                           name="copykeywords"
                                           @keyup.enter="btnsearch" placeholder="请输入查询条件">
                                </div>
                            </div>
                            <button id="Search" class="btn btn-default " @click.prevent="btnsearch">
                                <i class="fa fa-search"></i>查 询
                            </button>
                            <button id="Reset" class="btn btn-default " @click.prevent="btnreset">
                                <i class="fa fa-repeat"></i>重 置
                            </button>
                        </div>
                        <div class="form-group">
                            <div class="table-responsive">
                                <table class="table table-striped table-hover table-bordered  max-width-table"
                                       id="copyuserdatatable"
                                       style="">
                                    <thead>
                                    <tr role="row">
                                        <th style="width: 60px;" class="text-center">
                                            <label>
                                                序号
                                            </label>
                                        </th>
                                        <th style="width: 120px; " class="text-center Code_col"
                                            data-field="field:Account,cssClass:text-left,hasTitle:true">
                                            <label>
                                                账号
                                            </label>
                                        </th>
                                        <th class="text-center Name_col"
                                            data-field="field:RealName,cssClass:text-left,hasTitle:true,isTree:true">
                                            <label>
                                                姓名
                                            </label>
                                        </th>
                                        <th class="text-center Name_col"
                                            data-field="field:Mobile,cssClass:text-left,hasTitle:true">
                                            <label>
                                                手机
                                            </label>
                                        </th>
                                    </tr>
                                    </thead>
                                    <tbody>
                                    <tr>
                                        <td class="text-center Name_col" title="" style="width: 60px; height: 35px;"
                                            colspan="4">请输入查询条件
                                        </td>
                                    </tr>
                                    </tbody>
                                </table>
                            </div>
                        </div>
                    </div>
                    <div class="modal-footer">
                        <button type="button" class="btn btn-primary" data-dismiss="modal" @click.prevent="save">确定</button>
                        <button type="button" class="btn" data-dismiss="modal">关闭</button>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>
<style>
    @media screen and (max-width: 880px) {
        #ApprovalDetailPage{
           margin:10px 10px 0;
        }

    }
    body{
        overflow: unset !important;
    }
    /*@media screen and (max-width: 500px) {*/
        /*#ApprovalOpinionModal.footer{*/
            /*top:60% !important;*/
        /*}*/
    /*}*/
</style>
<script>
    import ApprovalProgressInfo from './ApprovalProgressInfo.vue'
    export default{
        data(){
            return {
                "pageType": 0,
                "isShowForm": true,
                "btns": [],
                "isShowButton": false,
                "serialNo": '',
                "processInstanceId": '',
                "isProcessing": 0,//是否已发起的流程，0:否，1:是
                "isShowPresetButton": false,
                "procInstance": {
                    "ID": "",
                    "Subject": "",
                    "Originator": "",
                    "OriginatorOrg": "",
                    "ActivityName": "",
                },
                basicFormInfo:{
                    "OriginatorID":"",
                    "OriginatorName":"",
                    "OriginatorOrgID":"",
                    "OriginatorOrgName":"",
                    "BusiTypeName":"",
                    "Folio":"",
                    "Subject":"",
                    "OriginatorOrgs": [],
                },
                "isDisabled": false,
                "UserID": '',
                "UserName": '',
                "SearchType": '',
            }
        },
        components: {
            'ApprovalProgressInfo': ApprovalProgressInfo,
        },
        props: {
            //流程自定义数据
            procData: {
                type: Object,
                required: true
            },
            //节点数据
            actData: {
                type: Object,
                required: true
            },
            //业务自定义数据
            busiData: {
                type: Object,
                required: true
            },
            //流程名称
            procName: {
                type: String,
                required: true
            },
            //用户绑定
            userHelp: {
                type: Object,
                required: false
            },
        },
        mounted(){
            var _this = this;
            var postData = {};
            if (_this.$route.query.SN) {
                _this.serialNo = _this.$route.query.SN;
                _this.isProcessing = 1;
                postData = {
                    "filter": JSON.stringify({
                        "SerialNo": _this.serialNo,
                    })
                };
            }
            else if (_this.$route.query.PIID) {
                _this.processInstanceId = _this.$route.query.PIID;
                _this.isProcessing = 1;
                postData = {
                    "filter": JSON.stringify({
                        "ProcessInstanceID": _this.processInstanceId,
                    })
                };
            }
            //已发起
            if (_this.isProcessing == 1) {
                var url = ServiceHost + "/api/invoke?SID=ACSrv-GetTaskItem";
                getDataSync(url, "post", postData, function (res) {
                    if (res.state > 0) {
                        var workTask = res.data;
                        _this.loadProcessForm(workTask.ProcessInstanceID);
                        _this.procInstance.ID = workTask.ProcessInstanceID;
                        _this.procInstance.Subject = workTask.Subject;
                        _this.procInstance.Originator = workTask.Originator;
                        _this.procInstance.ActivityName = workTask.ActivityName;
                        _this.$emit('getProcessInstance', _this.procInstance);
                        _this.$emit('getActData', workTask.ActDataFields);
                        _this.$emit('getProcData', workTask.ProcDataFields);
                        _this.pageType = workTask.OperatorType;
                        if (!_this.serialNo) {
                            _this.serialNo = workTask.SerialNo;
                        }
                        if (!_this.processInstanceId) {
                            _this.processInstanceId = workTask.ProcessInstanceID;
                        }
                        _this.pageType = workTask.OperatorType;
                        if (_this.pageType == '4') {
                            _this.isShowForm = false;
                        }
                        if (workTask.ProcStatus == '2') {
                            _this.isShowPresetButton = true;
                        }
                        if (workTask.ProcStatus == '3' || workTask.ProcStatus == '4') {
                            _this.isShowPresetButton = false;
                        }
                        if (workTask.Actions && workTask.Actions.length > 0) {
                            _this.btns = workTask.Actions;
                            _this.isShowButton = true;
                        } else {
                            _this.isShowButton = false;
                        }
                    } else {
                        _this.isShowForm = false;
                        _this.pageType = 4;
                        $("#NoPrivilege").html('')
                        NotifyError(res.errmsg);
                    }
                });
            }
            //未发起
            else {
                _this.loadProcessForm();
            }

            this.$nextTick(function () {
                //$("#ApprovalOriginator").val(_this.procInstance.Originator);
                this.IsDefaultFunc();
                if (this.pageType != '0') {
                    $("#form").find("input").prop("disabled", "disabled");
                    $("#form").find("textarea").prop("disabled", "disabled");
                    $("#form").find("select").prop("disabled", "disabled");
                    $("#form").find("button").prop("disabled", "disabled");
                    $("#form").find(".fa-calendar").parent("span").off("click");
                }
                $("#OrganizationId").val(this.basicFormInfo.OriginatorOrgID);
                $("#Organization").val(this.basicFormInfo.OriginatorOrgName);
                $("#Organization").attr("title",this.basicFormInfo.OriginatorOrgName);
            })
        },
//        updated(){
//            if (this.pageType != '0') {
//                $("#form").find("input").prop("disabled", "disabled");
//                $("#form").find("textarea").prop("disabled", "disabled");
//                $("#form").find("select").prop("disabled", "disabled");
//                $("#form").find("button").prop("disabled", "disabled");
//                $("#form").find(".fa-calendar").parent("span").off("click");
//            }
//        },
        methods: {
            getOrganization(){

            },
            //加载基本信息
            loadProcessForm: function (PIID) {
                var _this = this;
                var PostData = {};
                var Url = ServiceHost + "/api/invoke?SID=ACSrv-GetProcessFormInfo";
                if (_this.isProcessing == 0) {
                    _this.isShowPresetButton = true;
                    PostData = {
                        "param": JSON.stringify({
                            "ProcessName": _this.procName,
                        })
                    };
                } else {
                    PostData = {
                        "param": JSON.stringify({
                            "ProcessInstanceID": PIID,
                        })
                    };
                }
                getDataSync(Url, "post", PostData, function (res) {
                    if (res.state > 0) {
                        _this.basicFormInfo.OriginatorID = res.data.OriginatorID;
                        _this.basicFormInfo.OriginatorName = res.data.OriginatorName;
                        _this.basicFormInfo.BusiTypeName = res.data.BusiTypeName;
                        _this.basicFormInfo.Subject = res.data.Subject;
                        _this.basicFormInfo.Folio = res.data.Folio;
                        _this.basicFormInfo.OriginatorOrgs = res.data.OriginatorOrgs;
                        if(_this.isProcessing==0) {
                            if (localStorage.getItem("AC_DefaultOrg") != null) {
                                var DefaultOrg = JSON.parse(localStorage.getItem("AC_DefaultOrg"))[_this.basicFormInfo.OriginatorID];
                                $.each(_this.basicFormInfo.OriginatorOrgs, function (i, item) {
                                    if (item.ID == DefaultOrg) {
                                        _this.basicFormInfo.OriginatorOrgs[i].IsDefault = 1;
                                        _this.basicFormInfo.OriginatorOrgID = item.ID;
                                        _this.basicFormInfo.OriginatorOrgName = item.Name;
                                        $("#OrganizationId").val(_this.basicFormInfo.OriginatorOrgID);
                                        $("#Organization").val(_this.basicFormInfo.OriginatorOrgName);
                                    } else {
                                        _this.basicFormInfo.OriginatorOrgs[i].IsDefault = 0;
                                    }
                                });
                            } else {
                                $.each(_this.basicFormInfo.OriginatorOrgs, function (i, item) {
                                    _this.basicFormInfo.OriginatorOrgs[i].IsDefault = 0;
                                });
                            }
                        }else {
                            if(_this.basicFormInfo.OriginatorOrgs.length==1) {
                                _this.basicFormInfo.OriginatorOrgID = _this.basicFormInfo.OriginatorOrgs[0].ID;
                                _this.basicFormInfo.OriginatorOrgName = _this.basicFormInfo.OriginatorOrgs[0].Name;
                                $("#OrganizationId").val(_this.basicFormInfo.OriginatorOrgID);
                                $("#Organization").val(_this.basicFormInfo.OriginatorOrgName);
                            }
                        }
                        _this.procInstance.Subject = res.data.Subject;
                        _this.procInstance.Originator = res.data.OriginatorID;
                        _this.procInstance.OriginatorOrg = _this.basicFormInfo.OriginatorOrgID;
                    } else {
                        NotifyError(res.errmsg);
                    }
                });
            },
            //返回
            back: function () {
                this.$router.go(-1);
            },
            //提交审批
            btnSubmit: function () {
                $("#form").bootstrapValidator();
                var Datas = $("#form").data("bootstrapValidator");
                Datas.validate();
                var bool = Datas.isValid();
                if (bool == false) {
                    return false;
                }
                if ($("#OrganizationId").val() == '') {
                    NotifyWarning("所在部门不能为空");
                    return;
                }
                this.isDisabled = true;
                var _this = this;
                var postData = {
                    "newTask": JSON.stringify({
                        "Subject":  _this.basicFormInfo.Subject,
                        "OriginatorOrg": $("#OrganizationId").val(),
                        "ProcName": this.procName,
                        "ProcDataFields": this.procData,
                        "ActDataFields": this.actData,
                        "BusiDataFields": this.busiData,
                        "Comment": $("#Opinion").val()
                    })
                };
                var url = ServiceHost + "/api/invoke?SID=ACSrv-StartProcess";
                getDataAsync(url, "post", postData, function (res) {
                    if (res.state > 0) {
                        NotifySuccess("提交成功");
                        _this.back();

                    } else {
                        NotifyError(res.errmsg);
                        _this.isDisabled = false;
                    }
                });

            },
            //保存草稿
//            btnSave:function () {
//                var Datas = $("#form").data("bootstrapValidator");
//                Datas.validate();
//                var bool = Datas.isValid();
//                if (bool == false) {
//                    return false;
//                }
//                var _this=this;
//                var postData = {
//                    "entity": JSON.stringify({
//                        "ApprovalOriginator":$("#ApprovalOriginator").val(),
//                        "Organization":$("#Organization").val(),
//                        "BusiDesc":$("#BusiDesc").val(),
//                        "FormData":this.formData
//                    })
//                };
//                var url = ServiceHost + "/api/invoke?SID=";
//                getDataAsync(url, "post", postData, function (res) {
//                    if(res.state>0){
//                        NotifySuccess("保存成功");
//                        _this.LoadPageData();
//
//                    }else {
//                        NotifyError(res.errmsg);
//                    }
//                });
//            },
            //其他操作
            btnOperation: function (action) {
                var result=true;
                this.$emit('validateForm', action,function(str){
                    result=str;
                });
                if(!result){
                    return;
                }
                this.isDisabled = true;
                $('#ApprovalOpinionModal').on('shown.bs.modal', function () {
//                    debugger
                    var $this = $(this);
                    var dialog = $this.find('.modal-dialog');

                    //此种方式，在使用动画第一次显示时有问题
                    //解决方案，去掉动画fade样式
                    var top = ($(window).height() - dialog.height()) / 2;
                    dialog.css({
                        marginTop:top
                    });
                });
                $("#ApprovalOpinionModal").modal("show");
                $("#ModalTitle").text(action);
                $("#OperationVal").val(action);
                if (action == "转交") {
                    $("#TransferInput").show();
                } else {
                    $("#TransferInput").hide();
                }
                if (action == "撤销") {
                    $("#InputTitle").text("请填写撤销理由：");
                }
                if (action == "催办") {
                    $("#InputTitle").text("请填写提醒内容：");
                }

            },
            //审批意见模态框中的确定
            btnConfirm: function () {
                var _this = this;
                var action = $("#OperationVal").val();
                var opinion = $("#Opinion").val();
                var busiDesc = $("#BusiDesc").val();
                var sid = '';
                var postData = {};
                if (action == "转交") {
                    sid = 'RedirectAction';
                    postData = {
                        "destination": JSON.stringify({
                            "SerialNo": _this.serialNo,
                            "Users": _this.UserID.split(","),
                            "Comment": opinion
                        })
                    }
                } else if (action == "撤销") {
                    sid = 'CancelProcess';
                    postData = {
                        "process": JSON.stringify({
                            "SerialNo": _this.serialNo,
                            "Comment": opinion
                        })
                    }
                } else if (action == "催办") {
                    sid = 'RemindersAction';
                    postData = {
                        "remider": JSON.stringify({
                            "SerialNo": _this.serialNo,
                            "Comment": opinion
                        })
                    }
                } else {
                    sid = 'ExecuteAction';
                    postData = {
                        "action": JSON.stringify({
                            "ActionName": action,
                            "SerialNo": _this.serialNo,
                            "Subject": busiDesc,
                            "ProcName": this.procName,
                            "ProcDataFields": this.procData,
                            "ActDataFields": this.actData,
                            "BusiDataFields": this.busiData,
                            "Comment": opinion
                        })
                    }
                }
                var url = ServiceHost + "/api/invoke?SID=ACSrv-" + sid;
                getDataAsync(url, "post", postData, function (res) {
                    if (res.state > 0) {
                        NotifySuccess("执行成功");
                        _this.isDisabled = false;
                        $("#Opinion").val('')
                        if (window.location.href.lastIndexOf(("SN")) == -1) {
                            if( action != "催办") {
                                window.history.go(0);
                            }
                        }
                        else {
                            var formUrl = window.location.href;
                            formUrl = formUrl.substring(0, formUrl.lastIndexOf("SN")) + "PIID=" + _this.processInstanceId;
                            var url = new URL(formUrl);
                            var randomParam = (url.search == '' ? '?n=' : '&n=') + Math.random();
                            formUrl = url.origin + url.search + randomParam + "/" + url.hash + "&NoAuth=true";
                            window.location.href = formUrl;
                        }
                    } else {
                        NotifyError(res.errmsg);
                        _this.isDisabled = false;
                        $("#Opinion").val('');
                        _this.UserID = '';
                        _this.UserName = '';
                    }
                });
            },
            btnClose: function () {
                this.isDisabled = false;
                $("#Opinion").val('');
                this.UserID = '';
                this.UserName = '';
            },
            //所属组织
            OrganizationModal: function () {
                $('#OrganizationModal').modal('show');
                this.trClick();
                var $trs = $("#organizationdatatable tbody tr");
                var OrganizationId = $("#OrganizationId").val();
                var defOrgStorage= localStorage.getItem("AC_DefaultOrg");
                if(defOrgStorage!=null) {
                    var DefaultOrg = JSON.parse(defOrgStorage)[this.basicFormInfo.OriginatorID];
                    $trs.each(function () {
                        $(this).removeClass('selected');
                        $(this).find(".isDefault").removeAttr("checked");
                        if ($(this).find("input[name='Id']").val() == OrganizationId) {
                            $(this).addClass('selected');
                        }
                        if ($(this).find("input[name='Id']").val() == DefaultOrg) {
                            $(this).find(".isDefault").prop("checked", "checked")
                        }
                    });
                }
            },
            //是否默认所属组织
            IsDefaultFunc: function () {
                $(".isDefault").click(function () {
                    var $this = $(this);
                    if ($(".isDefault").is(':checked')) {
                        $(".isDefault").removeAttr("checked");
                        $(".isDefault").parents("tr").removeClass("selected");
                        $this.prop("checked", "checked");
                        $this.parents("tr").addClass("selected");
                    } else {
                        $this.parents("tr").removeClass("selected");
                    }
                })
            },
            //所属组织中的确定
            btnOrgConfirm: function () {
                var _this = this;
                var $sel = $("#organizationdatatable tr.selected");//选中的行
                var OrganizationId = $sel.find('input[name="Id"]').val();
                var OrganizationName = $sel.find('input[name="Name"]').val();
                var DefaultId = '';
                var DefaultOrg = {};
                var key = _this.basicFormInfo.OriginatorID;
                $(".isDefault:checked").each(function () {
                    DefaultId = $(this).parents("tr").find('input[name="Id"]').val();
                    DefaultOrg[key] = DefaultId;
                });
                localStorage.setItem("AC_DefaultOrg", JSON.stringify(DefaultOrg));
                $("#OrganizationId").val(OrganizationId);
                $("#Organization").val(OrganizationName);
                $("#Organization").attr("title",OrganizationName);
            },
            trClick: function () {
                var $trs = $("#organizationdatatable tbody tr");
                $trs.off("click").on("click", function (e) {
                    var val = !$(this).hasClass('selected');
                    if (val) {
                        $trs.each(function () {
                            $(this).removeClass('selected');
                        });
                        $(this).addClass('selected');
                    } else {
                        $(this).removeClass('selected');
                    }
                });
            },
            //转交人帮助
            UserModal: function () {
                $('#CopyUserModal').modal('show');
                var keywords = ($('#copykeywords').val());

                var userdatatabledata = $("#copyuserdatatable").data('bs.datagrid17');
                if (userdatatabledata) {
                    userdatatabledata.clearData();
                }

                if (keywords != '' || this.SearchType != '') {
                    this.SearchType = '';
                    $('#copykeywords').val('');
                } else {
                    if (this.userHelp && (this.userHelp.OrganizationID || this.userHelp.RoleID)) {
                        this.btnsearch();
                    }
                }
            },
            //选择模块帮助里的查询
            btnsearch: function () {
                var keywords = $.trim($('#copykeywords').val());
                var preset = this.userHelp && (this.userHelp.OrganizationID != null || this.userHelp.RoleID != null);//预置条件
                if (keywords == '' && !preset) {
                    NotifyWarning('请输入查询条件！');
                    return
                }
                this.LoadHelpPageData(keywords, this.SearchType);
            },
            //选择模块帮助的重置
            btnreset: function () {
                this.SearchType = '';
                $('#copykeywords').val('');
                $("#copyuserdatatable").data('bs.datagrid17').clearData();
                var preset = this.userHelp && (this.userHelp.OrganizationID != null || this.userHelp.RoleID != null);//预置条件
                if(preset){
                    this.btnsearch();
                }
            },
            LoadHelpPageData: function (keywords, SearchType) {
                var _this = this;
                let dataUrl = ServiceHost + "/api/invoke?SID=SYS_CommonHelper_GetUserList&help=true";
                var postData = {
                    "filter": {
                        "FieldName": SearchType,
                        "FieldValue": keywords,
                        'FilterKey': {
                            'NoDataAuth': true,
                            'ObjectId':  _this.userHelp && _this.userHelp.OrganizationID,
                            'RoleID': _this.userHelp && _this.userHelp.RoleID,
                            'Enabled': '1'
                        }
                    }
                };
                $("#copyuserdatatable").datagrid17({
                    showPagination: true,
                    url: dataUrl,
                    tableType: "single",
                    resizeCol: true,
                    maxRecordLimit: 10000,
                    PageSelectList: [5, 10, 15, 20, 25, 30, 35, 40, 45, 50, 100],
                    cookieEnable: false,
                    hiddenInps: [
                        {inpNm: "hidId", inpValue: "UserID"},
                        {inpNm: "hidName", inpValue: "Account"},
                        {inpNm: "hidMobile", inpValue: "Mobile"},
                    ],
                    success: function (rData) {
                    },
                    trdblclick: function () {
                        let id = $(this).find("input[name='hidId']").val();
                        let name = $(this).find("input[name='hidName']").val();
                        _this.UserID = id;
                        _this.UserName = name;
                        $('#hideTransferID').val(id);
                        $('#TransferName').val(name);
                        $('#CopyUserModal').modal('hide');
                    }

                }).data('bs.datagrid17').searchByFilter({searchCond: postData});
            },
            save: function () {
                var obj = $("#copyuserdatatable").data('bs.datagrid17');
                if (!obj) {
                    this.UserID = null;
                    this.UserName = null;
                } else {
                    let datasSel = obj.getSelectedRowData();
                    if (datasSel) {
                        var id = datasSel.UserID;
                        var name = datasSel.Account;
                        $('#hideTransferID').val(id);
                        $('#TransferName').val(name);
                        this.UserID = id;
                        this.UserName = name;
                    } else {
                        this.UserID = null;
                        this.UserName = null;
                    }
                }
            }
        }
    }
</script>