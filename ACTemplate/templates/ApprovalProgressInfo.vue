<template>
    <div class="ApprovalProgressPage">
        <div class="widget radius-bordered">
            <div class="well widget-body bordered-bottom" id="ApprovalInfoTitle" style="text-align:left;margin-bottom: 0px;" @click="show">
                <i class="fa fa-plus" style="color: #00a7cb;padding-right: 12px;cursor: pointer;"></i>审批进度信息
            </div>
            <div class="well widget-body" id="ApprovalInfo">
                <ul id="ApprovalLegend" style="list-style:none;overflow:hidden;">
                    <li style="border-right: 1px solid #ddd;padding-right: 22px">
                        <i class="fa fa-map-marker" style="margin-right: 5px;color: #6666FF"></i>当前节点
                    </li>
                    <li>
                        <div style="display: inline-block;border-bottom: 2px solid #71C2F9;width: 22px;margin:0 5px 3px 0;"></div>已审批
                    </li>
                    <li style="border-right: 1px solid #ddd;padding-right: 22px">
                        <div style="display: inline-block;border-bottom: 2px dashed #71C2F9;width: 22px;margin:0 5px 3px 0;"></div>未审批
                    </li>
                    <li>
                        <i class="fa fa-plus-circle" style="margin-right: 5px;color: #33CC99"></i>发起
                    </li>
                    <li>
                        <i class="fa fa-check-circle" style="margin-right: 5px;color: #33CC99"></i>审批通过
                    </li>
                    <li>
                        <i class="fa fa-times-circle" style="margin-right: 5px;color: #FF3333"></i>未通过/提前终止
                    </li>
                    <li style="border-right: 1px solid #ddd;padding-right: 22px">
                        <i class="fa fa-info-circle" style="margin-right: 5px;color: #FFCC66;"></i>待审批/下一节点/未到节点
                    </li>
                    <li>
                        <i class="fa fa-minus-circle" style="margin-right: 5px;color: #33CC99"></i>终节点
                    </li>
                    <li>
                        <i class="fa fa-minus-circle" style="margin-right: 5px;color: #FF3333"></i>异常终止节点
                    </li>

                </ul>
                <ul id="ApprovalProgress" style="overflow: hidden;list-style: none;">
                    <li v-for="(item,index) in AllDatas">
                        <!--不是终节点-->
                        <template v-if="item.NodeType!=3">
                            <template v-if="ProcStatus=='4'&&item.Status==2">
                                <div class="ProcNode active"></div>
                            </template>
                            <template v-else>
                                <div class="ProcNode">
                                <!--前半段进度线-->
                                <template v-if="item.Status==4">
                                    <div class="ProgressLineFront"></div>
                                </template>
                                <template v-else-if="item.Status==2">
                                    <div class="ProgressLineFront active"></div>
                                </template>
                                <template v-else>
                                    <div class="ProgressLineFront"></div>
                                </template>
                                <!--后半段进度线-->
                                <template v-if="index+1<AllDatas.length">
                                    <template v-if="ProcStatus=='4'">
                                        <div class="ProgressLineBack"></div>
                                    </template>
                                    <template v-else>
                                        <template v-if="AllDatas[index+1].Status==4">
                                            <div class="ProgressLineBack"></div>
                                        </template>
                                        <template v-else-if="AllDatas[index+1].Status==2">
                                            <div class="ProgressLineBack active"></div>
                                        </template>
                                        <template v-else>
                                            <div class="ProgressLineBack"></div>
                                        </template>
                                    </template>
                                </template>
                                <!--当前节点或终止节点图标-->
                                <template v-if="item.Status==2">
                                    <div class="NodeIcon active">
                                        <i class="fa fa-map-marker" style="font-size:18px;color: #6666FF;"></i>
                                    </div>
                                </template>
                                <template v-else>
                                    <div class="NodeIcon">
                                        <i class="fa fa-map-marker" style="font-size:18px;color: #6666FF;"></i>
                                    </div>
                                </template>
                                <!--审批节点名称-->
                                <div class="NodeName" style="font-size: 12px;">
                                    {{item.ActivityName}}
                                </div>
                                <!--头像-->
                                <img :src="item.Avatar" width="60" height="60" style="border-radius: 100%;margin: 5px 0;"/>
                                <!--审批状态图标-->
                                <template v-if="item.Status==4">
                                    <div class="StateIcon">
                                        <template v-if="item.ActionName=='拒绝'">
                                            <i class="fa fa-times-circle" style="color: #FF3333;"></i>
                                        </template>
                                        <template v-else>
                                            <i class="fa fa-check-circle" style="color: #33CC99;"></i>
                                        </template>
                                    </div>
                                </template>
                                <template v-else-if="item.Status==2">
                                    <div class="StateIcon">
                                        <i class="fa fa-info-circle" style="color: #FFCC66;"></i>
                                    </div>
                                </template>
                                <template v-else>
                                    <div class="StateIcon">
                                        <template v-if="item.ActionName=='撤销'">
                                            <i class="fa fa-times-circle" style="color: #FF3333;"></i>
                                        </template>
                                        <template v-else-if="item.ActionName=='完成'">
                                            <i class="fa fa-check-circle" style="color: #33CC99;"></i>
                                        </template>
                                        <template v-else>
                                            <i class="fa fa-plus-circle" style="color: #33CC99;"></i>
                                        </template>
                                    </div>

                                </template>
                                <!--用户名-->
                                <p>{{item.Destination}}</p>
                                <!--时间-->
                                <p style="font-size: 12px;">{{item.CreateTime}}</p>
                                <!--审批状态-->
                                <template v-if="item.Status==4">
                                    <p>{{item.ActionName}}<i class="fa fa-file-text" title="点击查看" style="margin-left: 5px;cursor: pointer;"@click="ViewDetail(item.ID)"></i></p>
                                </template>
                                <template v-else-if="item.Status==2">
                                    <p>待审批<i class="fa fa-file-text" title="点击查看" style="margin-left: 5px;cursor: pointer;"@click="ViewDetail(item.ID)"></i></p>
                                </template>
                                <template v-else>
                                    <p>{{item.ActionName}}</p>
                                </template>
                                </div>
                            </template>
                        </template>
                        <!--是终节点-->
                        <template v-else>
                            <div class="ProcNode">
                            <!--进度线-->
                            <div class="ProgressLineFront" style="width: 60px;"></div>
                            <div class="NodeIcon">
                                <i class="fa fa-map-marker" style="font-size:18px;color: #6666FF;"></i>
                            </div>
                            <!--审批节点名称-->
                            <div class="NodeName" style="font-size: 12px;margin-top: 14px;">
                                {{item.ActivityName}}
                            </div>
                            <!--终止节点图标-->
                            <template v-if="item.ActionName=='撤销'">
                                <div class="StateIcon" style="margin-top: 8px;">
                                    <i class="fa fa-minus-circle" style="color: #FF3333;font-size: 30px"></i>
                                </div>
                            </template>
                            <template v-else-if="item.ActionName=='完成'">
                                <div class="StateIcon" style="margin-top: 8px;">
                                    <i class="fa fa-minus-circle" style="color: #33CC99;font-size: 30px"></i>
                                </div>
                            </template>
                            <!--时间-->
                            <p style="font-size: 12px;">{{item.CreateTime}}</p>
                            <!--审批状态-->
                            <p>{{item.ActionName}}</p>
                            </div>
                        </template>

                    </li>
                </ul>
            </div>
        </div>
        <!--详细状态模态框-->
        <div class="modal fade in" id="DetailModal" tabindex="-1" role="dialog" aria-labelledby="myModalLabel"
             aria-hidden="false">
            <div class="modal-dialog">
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="close" data-dismiss="modal" aria-hidden="true">
                            ×
                        </button>
                        <h5 class="modal-title" id="myModalLabel">
                            详细状态
                        </h5>
                    </div>
                    <div class="modal-body row">
                        <div v-for="(item,index) in NodeLogs" style="margin-left: -35px;">
                            <div class="col-lg-12 col-sm-12 col-xs-12">
                                <div class="col-lg-3 col-sm-3 col-xs-3" style="padding-right: 0;text-align: right;">
                                    <!--头像-->
                                    <img :src="item.Avatar" width="35" height="35" style="border-radius: 100%;"/>
                                </div>
                                <div class="col-lg-5 col-sm-5 col-xs-5">
                                    <div style="margin-left: 10px;">
                                        <div style="padding-top: 10px;">
                                            <!--审批人-->
                                            {{item.Creator}}
                                            <!--审批状态-->
                                            <template v-if="item.ActionName=='拒绝'||item.ActionName=='不同意'||item.ActionName=='不通过'">
                                                <template v-if="item.ActionName=='拒绝'">
                                                    <span style="color:#FF3333">已{{item.ActionName}}</span>
                                                </template>
                                                <template v-else>
                                                    <span style="color:#FF3333">{{item.ActionName}}</span>
                                                </template>
                                            </template>
                                            <template v-else>
                                                <span style="color:#35B292">已{{item.ActionName}}</span>
                                            </template>
                                        </div>
                                    </div>
                                </div>
                                <div class="col-lg-4 col-sm-4 col-xs-4" style="padding-right: 0;padding-left: 0;">
                                    <div style="color: #C4C4C4;padding-top: 10px;">
                                        {{item.CreateTime}}
                                    </div>
                                </div>
                            </div>
                            <div class="col-lg-12 col-sm-12 col-xs-12" style="padding:0;">
                                <div class="col-lg-3 col-sm-3 col-xs-3">
                                </div>
                                <!--评论-->
                                <div class="col-lg-5 col-sm-5 col-xs-5" style="padding-right: 0;padding-left: 0;word-wrap:break-word;">
                                    <template v-if="index+1==NodeLogs.length">
                                        <div class="ApprovalComment" style="border-left: 2px dashed rgba(0,0,0,0);padding-left: 44px;margin-left: -11px;padding-bottom: 20px;font-size: 16px;">
                                            {{item.Comment}}
                                        </div>
                                    </template>
                                    <template v-else>
                                        <template v-if="item.Comment==''">
                                            <div class="ApprovalComment" style="border-left: 2px dashed #ccc;padding-left: 44px;margin-left: -11px;padding-bottom: 30px;font-size: 16px;">
                                                {{item.Comment}}
                                            </div>
                                        </template>
                                        <template v-else>
                                            <div class="ApprovalComment" style="border-left: 2px dashed #ccc;padding-left: 44px;margin-left: -11px;padding-bottom: 20px;font-size: 16px;">
                                                {{item.Comment}}
                                            </div>
                                        </template>
                                    </template>
                                </div>
                            </div>
                        </div>
                        <!--<ul id="NodeDetail">-->
                            <!--<li v-for="(item,index) in NodeLogs">-->
                                <!--<div class="" style="float: left">-->
                                    <!--&lt;!&ndash;头像&ndash;&gt;-->
                                    <!--<img :src="item.Avatar" width="40" height="40" style="border-radius: 100%;"/>-->
                                    <!--&lt;!&ndash;连接线&ndash;&gt;-->
                                    <!--&lt;!&ndash;<div class="ConnectLine"></div>&ndash;&gt;-->
                                    <!--<template v-if="index+1==NodeLogs.length">-->
                                    <!--</template>-->
                                    <!--<template v-else>-->
                                        <!--<div class="ConnectLine"></div>-->
                                    <!--</template>-->
                                <!--</div>-->
                                <!--<div style="float: left;margin-left: 10px;">-->
                                    <!--<div style="line-height: 40px;">-->
                                        <!--{{item.Creator}}-->
                                        <!--<template v-if="item.ActionName=='拒绝'||item.ActionName=='不同意'||item.ActionName=='不通过'">-->
                                            <!--<template v-if="item.ActionName=='拒绝'">-->
                                                <!--<span style="color:#FF3333">已{{item.ActionName}}</span>-->
                                            <!--</template>-->
                                            <!--<template v-else>-->
                                                <!--<span style="color:#FF3333">{{item.ActionName}}</span>-->
                                            <!--</template>-->
                                        <!--</template>-->
                                        <!--<template v-else>-->
                                            <!--<span style="color:#35B292">已{{item.ActionName}}</span>-->
                                        <!--</template>-->
                                    <!--</div>-->
                                    <!--<div class="Comment" style="font-size: 16px;width:240px;height: 100%;">{{item.Comment}}</div>-->
                                <!--</div>-->
                                <!--<div style="float: right;line-height: 40px;color: #C4C4C4">-->
                                    <!--{{item.CreateTime}}-->
                                <!--</div>-->
                            <!--</li>-->
                        <!--</ul>-->
                    </div>
                    <div class="modal-footer">
                        <button type="button" id="missModal" class="btn btn-primary" data-dismiss="modal">
                            关闭
                        </button>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>
<style>
    #ApprovalInfo{
        display: none;
    }
    #ApprovalInfo .StateIcon i.fa{
        font-size:20px;
    }
    #ApprovalLegend li{
        float: left;
        margin-right: 22px;
        padding: 5px 0;
    }
    #ApprovalProgress li{
        float: left;

        text-align: center;
        position: relative;
        overflow: hidden;
    }
    #ApprovalProgress li .ProcNode{
        padding:5px 16px;
    }
    #ApprovalProgress li .ProcNode.active{
        padding:5px 0;
    }
    /*#ApprovalProgress li:first-child{*/
        /*padding-left: 0px;*/
    /*}*/
    #ApprovalProgress li:first-child  .ProgressLineFront{
        display: none;
    }
    /*#ApprovalProgress li:first-child  .ProgressLineBack{*/
        /*left:92px;*/
    /*}*/
    #ApprovalProgress li p{
        margin-bottom: 0px;
    }
    #ApprovalProgress .ProgressLineFront{
        display: inline-block;
        border-bottom: 2px solid #71C2F9;
        width: 44px;
        position: absolute;
        top: 76px;
        left: 0px;
    }
    #ApprovalProgress .ProgressLineFront.active{
        border-bottom: 2px dashed #71C2F9;
    }
    #ApprovalProgress .ProgressLineBack{
        display: inline-block;
        border-bottom: 2px solid #71C2F9;
        width: 44px;
        position: absolute;
        top: 76px;
        left: 108px;
    }
    #ApprovalProgress .ProgressLineBack.active{
        border-bottom: 2px dashed #71C2F9;
    }
    #ApprovalProgress .NodeIcon{
        opacity: 0;
    }
    #ApprovalProgress .NodeIcon.active{
        opacity: 1;
    }
    @media screen and (max-width: 500px) {
        #DetailModal .modal-content{
            margin: 50% auto;
        }
        #ApprovalProgress .ProgressLineFront{
            width: 44px;
            left: -3px;
        }
        #ApprovalProgress .ProgressLineBack{
            width: 47px;
            left: 105px;
        }
    }
    @media screen and (max-width: 407px) {
        #DetailModal .ApprovalComment{
            margin-top: -10px;
            padding-top: 10px;
        }
    }
</style>
<script>
    export default{
        data(){
            return{
                "isShow":false,
                "AllDatas":[],
                "NodeLogs":[],
                "ProcStatus":''
            }
        },
        props: {
            ProgressId:{
                type: String,
                required: true,
            },

        },
        mounted(){
            var _this=this;
            this.$nextTick(function () {
                _this.LoadData();
            })
        },
        methods:{
            show:function(){
                if(this.isShow){
                    $("#ApprovalInfoTitle").html('<i class="fa fa-plus" style="color: #00a7cb;padding-right: 12px;cursor: pointer;"></i>审批进度信息');
                    $("#ApprovalInfo").slideUp();
                    this.isShow=false;
                }else{
                    $("#ApprovalInfoTitle").html('<i class="fa fa-minus" style="color: #00a7cb;padding-right: 12px;cursor: pointer;"></i>审批进度信息');
                    $("#ApprovalInfo").slideDown();
                    this.isShow=true;
                }

                $("#ApprovalInfo").show();
            },
            LoadData:function () {
                if(this.ProgressId){
                    var dataUrl = ServiceHost + "/api/invoke?SID=ACSrv-GetWorkflowViews";
                    let _this = this;
                    getDataAsync(dataUrl, "Get", {"processInstanceId":_this.ProgressId}, function (result) {
                        if(result.state>0){
                            _this.ProcStatus=result.data.ProcessInstanceStatus;
                            _this.AllDatas=result.data.Nodes;
                            $.each(_this.AllDatas, function(index, val){
                                val.CreateTime=val.CreateTime.replace('T',' ').substring(0,19)
                            });
                        } else{
                            NotifyError(result.errmsg);
                        }

                    });
                }

            },
            ViewDetail:function (ID) {
                $("#DetailModal").modal("show");
                var dataUrl = ServiceHost + "/api/invoke?SID=ACSrv-GetWorkflowNodeLogs";
                let _this = this;
                getDataAsync(dataUrl, "Get", {"workTaskId":ID}, function (result) {
                    if(result.state>0){
                        _this.NodeLogs=result.data;
                        $.each(_this.NodeLogs, function(index, val){
                            val.CreateTime=val.CreateTime.replace('T',' ').substring(0,19)
                        });
                    } else{
                        NotifyError(result.errmsg);
                    }

                });
            }
        }
    }

</script>