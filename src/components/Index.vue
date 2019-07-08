<template>
    <div>
        <!-- JS-API实时控制台: https://wsdebug.dingtalk.com/ -->
        <!-- 几个测试按钮 -->
        <div class="func">
            <Button type="Primary" @click="runtimePermissionRequestAuthCode">获取免登授权码</Button>
            <span >{{authcode}}</span>
        </div>
        <div class="func">
            <Button type="Primary" @click="deviceBaseGetPhoneInfo">获取手机基础信息</Button>
            <span >{{phoneInfo}}</span>
        </div>
        <div class="func">
            <Button type="Primary" @click="bizContactComplexPicker">通讯录选人</Button>
            <span >{{complexPicker}}</span>
        </div>
        <div><span>{{err}}</span></div>
        <div><span>{{info}}</span></div>
    </div>
</template>
<style>
    .func{margin:30px}
</style>

<script>
import * as dd from 'dingtalk-jsapi'

var app = null
//企业ID
const corpId = "ding8ac2bab2b708b3cc35c2f4657eb6378f"
//应用ID
const agentId = "273673341"
//服务端生成，doc: https://open-doc.dingtalk.com/microapp/dev/uwa7vs
const timeStamp = "1562580799079"
const nonceStr = "abcdefg"
const signature = "773effca30f9b5f7bd3f918190812682bcc5a65c"

export default {
    
    data(){
        return{
            authcode: '',
            phoneInfo: '',
            complexPicker: '',
            err: ''
        }
    },
    methods: {
        runtimePermissionRequestAuthCode(){
            dd.runtime.permission.requestAuthCode({
                corpId: corpId,
                onSuccess: function(result) {
                    app.$data.authcode = result
                },
                onFail : function(err) {
                    app.$data.err = err
                }
            })
        },
        deviceBaseGetPhoneInfo(){
            dd.device.base.getPhoneInfo({
                onSuccess : function(result) {
                    app.$data.phoneInfo = result
                },
                onFail : function(err) {
                    app.$data.err = err
                }
            });
        },
        bizContactComplexPicker(){
            dd.ready(function() {
                dd.biz.contact.complexPicker({
                    title:"选择部门",
                    corpId:corpId,
                    multiple:true,            //是否多选
                    limitTips:"超出了",          //超过限定人数返回提示
                    maxUsers:1000,            //最大可选人数
                    pickedUsers:[],            //已选用户
                    pickedDepartments:[],          //已选部门
                    disabledUsers:[],            //不可选用户
                    disabledDepartments:[],        //不可选部门
                    requiredUsers:[],            //必选用户（不可取消选中状态）
                    requiredDepartments:[],        //必选部门（不可取消选中状态）
                    appId:158,              //微应用的Id
                    responseUserOnly:false,        //返回人，或者返回人和部门
                    startWithDepartmentId:0 ,   //仅支持0和-1
                    onSuccess: function(result) {
                        app.$data.complexPicker = result
                    },
                    onFail : function(err) {
                        app.$data.err = err
                    }
                });
            });
        }
    },
    created() {
        dd.config({
            agentId: agentId, // 必填，微应用ID
            corpId: corpId,     //必填，企业ID
            timeStamp: timeStamp, // 必填，生成签名的时间戳
            nonceStr: nonceStr, // 必填，生成签名的随机串
            signature: signature, // 必填，签名
            type: 1,   //选填。0表示微应用的jsapi,1表示服务窗的jsapi；不填默认为0。该参数从dingtalk.js的0.8.3版本开始支持
            jsApiList : [
                'runtime.info',
                'biz.contact.choose',
                'device.notification.confirm',
                'device.notification.alert',
                'device.notification.prompt',
                'biz.ding.post',
                'biz.util.openLink',
                'biz.contact.complexPicker',
            ] // 必填，需要使用的jsapi列表，注意：不要带dd。
        });

        app = this;
    },
}
</script>