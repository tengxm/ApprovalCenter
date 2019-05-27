<template>
    <ApprovalDetailPage :busiData="busiData" :procName="procName" :procData="procData" :actData="actData" :multiProcData="multiProcData" :userHelp="userHelp"
                        @getProcessInstance="getProcessInstance" @getActData="getActData" @getProcData="getProcData" @validateForm="validateForm">
        <!--自定义按钮区（发起审批页面）-->
        <div slot="BusiButton" class="widget-buttons buttons-bordered " style="border:none;">
            <button title="自定义" type="button" class="btn btn-default">
                <i class="fa fa-arrow-up"></i>自定义
            </button>
        </div>
        <!--业务表单-->
        <div slot="FormContent">
            <h5 class="row-title before-color">
                <i class="fa fa-file-text iconcolor"></i>
                表单信息
            </h5>
            <div class="row">

            </div>
        </div>
    </ApprovalDetailPage>
</template>
<script>
    import ApprovalDetailPage from 'approvalcenter/ACTemplate/templates/ApprovalFormPage.vue'
    export default{
        data(){
            return{
                procName:'',
                busiData:{},
                procData:{},
                actData:{},
                multiProcData:[],
                procInstance:{},
                ApprovalBtn:''
            }
        },
        mounted(){
            //获取业务表单信息
            if(this.procInstance.ID){
                var url = ServiceHost + "/api/invoke?SID=ACSrv-GetLeaveData";
                var postData={
                    "processInstanceId":this.procInstance.ID
                }
                var _this = this;
                getDataAsync(url, "post", postData, function (res) {
                    if(res.state>0){
                        _this.busiData=res.data;
                    }else {
                        NotifyError(res.errmsg);
                    }
                });
            }
            //表单发起多个实例（可自行设置发起实例的数量）
            // for(var i=0;i<2;i++){
            //     this.multiProcData.push(this.procData);
            // }
            this.$nextTick(function () {});
        },
        components: {
            'ApprovalDetailPage': ApprovalDetailPage
        },
        methods:{
            //获取流程实例
            getProcessInstance:function (val) {
                this.procInstance=val;
            },
            //获取节点数据
            getActData:function (val) {
                this.actData=val;
            },
            //获取流程自定义数据
            getProcData:function (val) {
                this.procData=val;
            },
            //审批时点击按钮进行表单验证的方法
            validateForm:function(val){
                this.ApprovalBtn=val;
                if(this.ApprovalBtn=='同意'){
                    $("#form").bootstrapValidator();
                    var Datas = $("#form").data("bootstrapValidator");
                    Datas.validate();
                    var bool = Datas.isValid();
                    callback(bool);
                }
            },
        }
    }
</script>