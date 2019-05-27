<template>
    <div class="ApprovalProgressPage">
        <div class="widget radius-bordered">
            <div class="well widget-body bordered-bottom" id="ApprovalInfoTitle" style="text-align:left;margin-bottom: 0px;" @click="show">
                <i class="fa fa-plus" style="color: #00a7cb;padding-right: 12px;cursor: pointer;"></i>审批进度信息
                <template v-if="ProcStatus==2">- 进行中</template>
                <template v-else-if="ProcStatus==3">- 已完成</template>
                <template v-else-if="ProcStatus==4">- 已撤销</template>
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
                        <i class="fa fa-minus-circle" style="margin-right: 5px;color: #FF3333"></i>撤销终止节点
                    </li>

                </ul>
                <ul id="ApprovalProgress" style="overflow: hidden;list-style: none;">
                    <li v-for="(item,index) in AllDatas">
                        <!--不是终节点-->
                        <template v-if="item.ActivityName!='结束'">
                            <!--一人审批-->
                            <template v-if="item.data.length==1">
                                <div class="ProcNode">
                                    <!--当前节点或终止节点图标-->
                                    <template v-if="item.data[0].Status==2">
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
                                        {{item.data[0].ActivityName}}
                                    </div>
                                    <!--前半段进度线-->
                                    <template v-if="index==AllDatas.length-1">
                                        <div class="ProgressLineFront active"></div>
                                    </template>
                                    <template v-else>
                                        <div class="ProgressLineFront"></div>
                                    </template>
                                    <!--头像-->
                                    <template v-if="item.data[0].Avatar">
                                        <img :src="item.data[0].Avatar" width="61" height="61" style="border-radius: 100%;margin: 5px 0;"/>
                                    </template>
                                    <template v-else>
                                        <div style="width: 61px;height: 61px;border-radius: 100%;color: #fff;background-color: #00a7cb;line-height: 58px;margin: 5px 0;display: inline-block;cursor: default;">{{item.data[0].Destination[item.data[0].Destination.length-2]}}{{item.data[0].Destination[item.data[0].Destination.length-1]}}</div>
                                    </template>
                                    <!--后半段进度线-->
                                    <template v-if="index+1<AllDatas.length">
                                        <template v-if="ProcStatus=='4'">
                                            <div class="ProgressLineBack"></div>
                                        </template>
                                        <template v-else>
                                            <template v-if="(index+1)==AllDatas.length-1">
                                                <template v-if="AllDatas[index+1].ActivityName=='结束'">
                                                    <div class="ProgressLineBack"></div>
                                                </template>
                                                <template v-else>
                                                    <div class="ProgressLineBack active"></div>
                                                </template>
                                            </template>
                                            <template v-else>
                                                <div class="ProgressLineBack"></div>
                                            </template>
                                        </template>
                                    </template>
                                    <template v-else>
                                        <div class="ProgressLineBack" style="opacity: 0"></div>
                                    </template>
                                    <!--审批状态图标-->
                                    <template v-if="item.data[0].Status==4">
                                        <div class="StateIcon">
                                            <template v-if="item.data[0].ActionName=='拒绝'">
                                                <i class="fa fa-times-circle" style="color: #FF3333;"></i>
                                            </template>
                                            <template v-else>
                                                <i class="fa fa-check-circle" style="color: #33CC99;"></i>
                                            </template>
                                        </div>
                                    </template>
                                    <template v-else-if="item.data[0].Status==2">
                                        <div class="StateIcon">
                                            <i class="fa fa-info-circle" style="color: #FFCC66;"></i>
                                        </div>
                                    </template>
                                    <template v-else>
                                        <div class="StateIcon">
                                            <template v-if="item.data[0].ActionName=='撤销'">
                                                <i class="fa fa-times-circle" style="color: #FF3333;"></i>
                                            </template>
                                            <template v-else-if="item.data[0].ActionName=='完成'">
                                                <i class="fa fa-check-circle" style="color: #33CC99;"></i>
                                            </template>
                                            <template v-else>
                                                <i class="fa fa-plus-circle" style="color: #33CC99;"></i>
                                            </template>
                                        </div>

                                    </template>
                                    <!--用户名-->
                                    <p style="width: 156px;">{{item.data[0].Destination}}</p>
                                    <!--时间-->
                                    <p style="font-size: 12px;width: 156px;">{{item.data[0].CreateTime}}</p>
                                    <!--审批状态-->
                                    <template v-if="item.data[0].Status==4">
                                        <p>{{item.data[0].ActionName}}<i class="fa fa-file-text" title="点击查看" style="margin-left: 5px;cursor: pointer;"@click="ViewDetail(item.data[0].ID,index)"></i></p>
                                    </template>
                                    <template v-else-if="item.data[0].Status==2">
                                        <p>待审批<i class="fa fa-file-text" title="点击查看" style="margin-left: 5px;cursor: pointer;"@click="ViewDetail(item.data[0].ID,index)"></i></p>
                                    </template>
                                    <template v-else>
                                        <p>{{item.data[0].ActionName}}</p>
                                    </template>
                                </div>
                            </template>
                            <!--多人审批-->
                            <template v-else>
                                <div class="ProcNode">
                                    <!--当前节点或终止节点图标-->
                                    <template v-if="index==AllDatas.length-1">
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
                                        {{item.data[0].ActivityName}}
                                    </div>
                                    <!--前半段进度线-->
                                    <template v-if="index==AllDatas.length-1">
                                        <div class="ProgressLineFront active"></div>
                                    </template>
                                    <template v-else>
                                        <div class="ProgressLineFront"></div>
                                    </template>
                                    <!--头像-->
                                    <template v-if="item.data[0].Avatar">
                                        <img class="multiApproval" :src="item.data[0].Avatar" width="61" height="61" style="border-radius: 100%;margin: 5px 0;" @click="MultiModal(item.ActivityName,item.ParentID)"/>
                                    </template>
                                    <template v-else>
                                        <div class="multiApproval" style="width: 61px;height: 61px;border-radius: 100%;color: #fff;background-color: #00a7cb;line-height: 58px;margin: 5px 0;display: inline-block;cursor: pointer;" @click="MultiModal(item.ActivityName,item.ParentID)">{{item.data[0].Destination[item.data[0].Destination.length-2]}}{{item.data[0].Destination[item.data[0].Destination.length-1]}}</div>
                                    </template>
                                    <!--后半段进度线-->
                                    <template v-if="index+1<AllDatas.length">
                                        <template v-if="ProcStatus=='4'">
                                            <div class="ProgressLineBack" style="margin-left: 15px;"></div>
                                        </template>
                                        <template v-else>
                                            <template v-if="(index+1)==AllDatas.length-1">
                                                <template v-if="AllDatas[index+1].ActivityName=='结束'">
                                                    <div class="ProgressLineBack" style="margin-left: 15px;"></div>
                                                </template>
                                                <template v-else>
                                                    <div class="ProgressLineBack active" style="margin-left: 15px;"></div>
                                                </template>
                                            </template>
                                            <template v-else>
                                                <div class="ProgressLineBack" style="margin-left: 15px;"></div>
                                            </template>
                                        </template>
                                    </template>
                                    <template v-else>
                                        <div class="ProgressLineBack" style="opacity: 0"></div>
                                    </template>
                                    <!--审批状态图标-->
                                    <template v-if="index==AllDatas.length-1">
                                        <div class="StateIcon">
                                            <i class="fa fa-info-circle" style="color: #FFCC66;"></i>
                                        </div>
                                    </template>
                                    <template v-else>
                                        <template v-if="item.data[item.data.length-1].Status==4">
                                            <div class="StateIcon">
                                                <template v-if="item.data[item.data.length-1].ActionName=='拒绝'">
                                                    <i class="fa fa-times-circle" style="color: #FF3333;"></i>
                                                </template>
                                                <template v-else>
                                                    <i class="fa fa-check-circle" style="color: #33CC99;"></i>
                                                </template>
                                            </div>
                                        </template>
                                        <template v-else-if="item.data[item.data.length-1].Status==2">
                                            <div class="StateIcon">
                                                <i class="fa fa-info-circle" style="color: #FFCC66;"></i>
                                            </div>
                                        </template>
                                        <template v-else>
                                            <div class="StateIcon">
                                                <template v-if="item.data[item.data.length-1].ActionName=='撤销'">
                                                    <i class="fa fa-times-circle" style="color: #FF3333;"></i>
                                                </template>
                                                <template v-else-if="item.data[item.data.length-1].ActionName=='完成'">
                                                    <i class="fa fa-check-circle" style="color: #33CC99;"></i>
                                                </template>
                                                <template v-else>
                                                    <i class="fa fa-plus-circle" style="color: #33CC99;"></i>
                                                </template>
                                            </div>
                                        </template>
                                    </template>
                                    <!--用户名-->
                                    <p style="width: 156px;">{{item.data[0].Destination}}等{{item.data.length}}人</p>
                                    <!--时间-->
                                    <p style="font-size: 12px;width: 156px;">{{item.data[item.data.length-1].CreateTime}}</p>
                                    <!--审批状态-->
                                    <p>查看详情<i class="fa fa-file-text" title="点击查看" style="margin-left: 5px;cursor: pointer;" @click="MultiModal(item.ActivityName,item.ParentID)"></i></p>

                                </div>
                            </template>
                        </template>
                        <!--是终节点-->
                        <template v-else>
                            <div class="ProcNode">
                                <div class="NodeIcon">
                                    <i class="fa fa-map-marker" style="font-size:18px;color: #6666FF;"></i>
                                </div>
                                <!--审批节点名称-->
                                <div class="NodeName" style="font-size: 12px;margin-top: 14px;">
                                    {{item.ActivityName}}
                                </div>
                                <!--进度线-->
                                <!--<div class="ProgressLineFront" style="width: 60px; display: inline-block"></div>-->
                                <!--终止节点图标-->
                                <div style="overflow: hidden">
                                    <template v-if="item.data[0].ActionName=='撤销'">
                                        <div class="FinalLine"></div>
                                        <div class="StateIcon" style="float: left;margin-top:8px;margin-left: 5px;">
                                            <i class="fa fa-minus-circle" style="color: #FF3333;font-size: 30px"></i>
                                        </div>
                                        <div class="FinalLine" style="opacity: 0;"></div>
                                    </template>
                                    <template v-else-if="item.data[0].ActionName=='完成'">
                                        <div class="FinalLine"></div>
                                        <div class="StateIcon" style="float: left;margin-top:8px;margin-left: 5px;">
                                            <i class="fa fa-minus-circle" style="color: #33CC99;font-size: 30px"></i>
                                        </div>
                                        <div class="FinalLine" style="opacity: 0;"></div>
                                    </template>
                                </div>

                                <!--时间-->
                                <p style="font-size: 12px;width: 155px;">{{item.data[0].CreateTime}}</p>
                                <!--审批状态-->
                                <p>{{item.data[0].ActionName}}</p>
                            </div>
                        </template>
                    </li>
                </ul>
            </div>
        </div>
        <!--详细状态模态框-->
        <div class="modal fade in" id="DetailModal" tabindex="-1" role="dialog" aria-labelledby="myModalLabel"
             aria-hidden="false" style="z-index: 99999;">
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
                                    <template v-if="item.Avatar">
                                        <img :src="item.Avatar" width="35" height="35" style="border-radius: 100%;"/>
                                    </template>
                                    <template v-else>
                                        <div style="width: 35px;height: 35px;border-radius: 100%;color: #fff;background-color: #00a7cb;line-height: 35px;margin: 5px 0;display: inline-block;cursor: default;text-align: center">{{item.Creator[item.Creator.length-2]}}{{item.Creator[item.Creator.length-1]}}</div>
                                    </template>
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
                    </div>
                    <div class="modal-footer">
                        <button type="button" id="missModal" class="btn btn-primary" data-dismiss="modal">
                            关闭
                        </button>
                    </div>
                </div>
            </div>
        </div>
        <!--多人审批modal-->
        <div class="modal fade in" id="MultiModal" tabindex="-1" role="dialog" aria-labelledby="MultiModalLabel"
             aria-hidden="false">
            <div class="modal-dialog">
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="close" data-dismiss="modal" aria-hidden="true">
                            ×
                        </button>
                        <h5 class="modal-title" id="MultiModalLabel">
                            多人审批状态
                        </h5>
                    </div>
                    <div class="modal-body row">
                        <ul id="MultiApproval" style="overflow: hidden;list-style: none;">
                            <li v-for="(item,index) in MultiApproval">
                                <div class="ProcNode">
                                    <!--头像-->
                                    <template v-if="item.Avatar">
                                        <img :src="item.Avatar" width="61" height="61" style="border-radius: 100%;margin: 5px 0;"/>
                                    </template>
                                    <template v-else>
                                        <div style="width: 61px;height: 61px;border-radius: 100%;color: #fff;background-color: #00a7cb;line-height: 58px;margin: 5px 0;display: inline-block;cursor: default;">{{item.Destination[item.Destination.length-2]}}{{item.Destination[item.Destination.length-1]}}</div>
                                    </template>
                                    <!--<img :src="item.Avatar" width="61" height="61" style="border-radius: 100%;margin: 5px 0;"/>-->
                                    <!--审批状态图标-->
                                    <template v-if="item.Status==4">
                                        <div class="StateIcon">
                                            <template v-if="item.ActionName=='拒绝'">
                                                <i class="fa fa-times-circle" style="color: #FF3333;font-size: 18px;"></i>
                                            </template>
                                            <template v-else>
                                                <i class="fa fa-check-circle" style="color: #33CC99;font-size: 18px;"></i>
                                            </template>
                                        </div>
                                    </template>
                                    <template v-else-if="item.Status==2">
                                        <div class="StateIcon">
                                            <i class="fa fa-info-circle" style="color: #FFCC66;font-size: 18px;"></i>
                                        </div>
                                    </template>
                                    <template v-else>
                                        <div class="StateIcon">
                                            <template v-if="item.ActionName=='撤销'">
                                                <i class="fa fa-times-circle" style="color: #FF3333;font-size: 18px;"></i>
                                            </template>
                                            <template v-else-if="item.ActionName=='完成'">
                                                <i class="fa fa-check-circle" style="color: #33CC99;font-size: 18px;"></i>
                                            </template>
                                            <template v-else>
                                                <i class="fa fa-plus-circle" style="color: #33CC99;font-size: 18px;"></i>
                                            </template>
                                        </div>
                                    </template>
                                    <!--用户名-->
                                    <p style="width: 156px;">{{item.Destination}}</p>
                                    <!--时间-->
                                    <p style="font-size: 12px;width: 156px;">{{item.CreateTime}}</p>
                                    <!--审批状态-->
                                    <template v-if="item.Status==4">
                                        <p>{{item.ActionName}}<i class="fa fa-file-text" title="点击查看" style="margin-left: 5px;cursor: pointer;"@click="ViewDetail(item.ID,index)"></i></p>
                                    </template>
                                    <template v-else-if="item.Status==2">
                                        <p>待审批<i class="fa fa-file-text" title="点击查看" style="margin-left: 5px;cursor: pointer;"@click="ViewDetail(item.ID,index)"></i></p>
                                    </template>
                                    <template v-else>
                                        <p>{{item.ActionName}}</p>
                                    </template>
                                </div>
                            </li>
                        </ul>
                    </div>
                    <div class="modal-footer">
                        <button type="button" class="btn btn-primary" data-dismiss="modal">
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
    #ApprovalProgress li .ProcNode.active{
        padding:5px 0;
    }
    #ApprovalProgress li:first-child  .ProgressLineFront{
        opacity:0;
    }
    #ApprovalProgress li p{
        margin-bottom: 0px;
    }
    #ApprovalProgress .ProgressLineFront{
        display: inline-block;
        border-bottom: 2px solid #71C2F9;
        width: 44px;
    }
    #ApprovalProgress .ProgressLineFront.active{
        border-bottom: 2px dashed #71C2F9;
    }
    #ApprovalProgress .ProgressLineBack{
        display: inline-block;
        border-bottom: 2px solid #71C2F9;
        width: 44px;
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
    .FinalLine{
        border-bottom: 2px solid #71C2F9;
        width: 60px;
        float: left;
        margin-top:23px;
    }
    @media screen and (max-width: 407px) {
        #DetailModal .ApprovalComment{
            margin-top: -10px;
            padding-top: 10px;
        }
    }
    .multiApproval{
        box-shadow:4px 1px 0 rgb(255,255,255),5px 1px 0 rgba(0,0,0,0.4),
        9px 1px 0px rgb(255,255,255),10px 1px 0px rgba(0,0,0,0.4),
        14px 1px 0px rgb(255,255,255),15px 1px 0 rgba(0,0,0,0.4);
        cursor: pointer;
    }
    #MultiApproval li{
        float: left;
        text-align: center;
        overflow: hidden;
    }
    #MultiApproval li p{
        margin-bottom: 0px;
    }
</style>
<script>
    export default{
        data(){
            return{
                "isShow":false,
                "AllDatas":[],
                "NodeLogs":[],
                "ProcStatus":'',
                "MultiApproval":[],
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
                var ProcStatus='';
                if(this.ProcStatus==2){
                    ProcStatus=' - 进行中'
                }else if(this.ProcStatus==3){
                    ProcStatus=' - 已完成'
                }else if(this.ProcStatus==4){
                    ProcStatus=' - 已撤销'
                }
                if(this.isShow){
                    $("#ApprovalInfoTitle").html('<i class="fa fa-plus" style="color: #00a7cb;padding-right: 12px;cursor: pointer;"></i>审批进度信息'+ProcStatus);
                    $("#ApprovalInfo").slideUp();
                    this.isShow=false;
                }else{
                    $("#ApprovalInfoTitle").html('<i class="fa fa-minus" style="color: #00a7cb;padding-right: 12px;cursor: pointer;"></i>审批进度信息'+ProcStatus);
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
                            var Nodes=result.data.Nodes;
                            var multi=[];
                            var obj={};
                            for (var i=0;i<Nodes.length;i++){
                                for (var a = 0; a < multi.length; a++) {
                                    $.each(multi[a].data, function(index, val){
                                        if (val.ID == Nodes[i].ParentID){
                                            if(val.ActivityName == Nodes[i].ActivityName){
                                                multi[a].data.push(Nodes[i]);
                                                obj[Nodes[i].ParentID] = Nodes[i].ParentID;
                                            }
                                        }
                                    });
                                }
                                if(!obj[Nodes[i].ParentID]||!obj[Nodes[i].ActivityName]){
                                    multi.push({
                                        ParentID:Nodes[i].ParentID,
                                        ActivityName: Nodes[i].ActivityName,
                                        data: [Nodes[i]]
                                    });
                                    obj[Nodes[i].ParentID] = Nodes[i].ParentID;
                                    obj[Nodes[i].ActivityName] = Nodes[i].ActivityName;
                                }else{
                                    for(var j = 0; j < multi.length; j++){
                                        if(multi[j].ParentID == Nodes[i].ParentID){
                                            if(multi[j].ActivityName == Nodes[i].ActivityName){
                                                multi[j].data.push(Nodes[i]);
                                                break;
                                            }
                                        }

                                    }
                                }
                            }
                            _this.AllDatas=multi;
                            // _this.AllDatas=result.data.Nodes;
                            $.each(_this.AllDatas, function(index, val){
                                $.each(val.data, function(i, value){
                                    value.CreateTime=value.CreateTime.replace('T',' ').substring(0,19);
                                });
                            });
                        } else{
                            NotifyError(result.errmsg);
                        }

                    });
                }

            },
            ViewDetail:function (ID,index) {
                $("#DetailModal").modal("show");
                var u = navigator.userAgent;
                var isIOS = !!u.match(/\(i[^;]+;( U;)? CPU.+Mac OS X/); //ios终端
                if (isIOS) {
                    var top=index*230+'px';
//                    $("#DetailModal").css({"top":top,"bottom":"unset"})
                    $("#DetailModal .modal-content").css({"margin-top":top})
                }
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
            },
            MultiModal:function (ActivityName,ParentID) {
                var _this=this;
                $("#MultiModal").modal('show');
                $.each(_this.AllDatas, function(index, val){
                    if(val.ActivityName==ActivityName&&val.ParentID==ParentID){
                        _this.MultiApproval=val.data;
                    }
                });
            }
        }
    }

</script>