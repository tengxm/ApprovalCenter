<template>
    <ApprovalDetailPage :busiData="busiData" :procName="procName" :procData="procData" :actData="actData"
                        @getProcessInstance="getProcessInstance" @getActData="getActData" @getProcData="getProcData" @validateForm="validateForm">
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