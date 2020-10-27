<template>
<view>
<snav :title="stitle">
  <view class="back" @tap.stop="backClick">
    <image class="back_kmg" src="/static/pages/image/back.png"></image>
  </view>
  <view class="delete_button" @click="deleteConversation()">
        删除会话
  </view>
</snav>
<view class="container" :style="'padding-top:' + navHeight + 'px'">
  <scroll-view class="contentcontainer" :style="'height:' + wh + 'px'" scroll-y :scroll-top="scrolltop">
    <block v-for="(message, index) in messages" :key="index">
      <message :message="message"></message>
    </block>
  </scroll-view>
  <view class="inputer">
    <image class="voice" src="/static/pages/image/voice.png" @tap="voiceHandler"></image>
    <input v-if="!showvoice" @input="inputChangeHandler" placeholder="type a message!" class="input" type="text" confirm-type="send" @confirm="sendMessageHandler" :value="inputValue"></input>
    <view v-else class="recorder" @touchstart="startRecord" @touchend="stopRecord">
      <text>{{recordTxt}}</text>
    </view>
  </view>
</view>
</view>
</template>

<script>
//index.js
//获取应用实例
import { toLong } from "../../third/tools";
import message from "./message/index";

export default {
  data() {
    return {
      messages: [],
      uid: 0,
      scrolltop: 999999,
      inputValue: '',
      showing: false,
      stitle: '',
      wh: 0,
      navHeight: 0,
      showvoice: false,
      recordTxt: '按下录音',
      startTime: 0,
      duration: 0,
      timer: null,
      recordFile: ''
    };
  },

  components: {
    message
  },
  props: {},
  onShow: function () {
    getApp().getIM().rosterManage.readRosterMessage(this.uid);
    this.setData({
      showing: true
    });
  },
  onUnload: function () {
    this.setData({
      showing: false
    });
	
	const im = getApp().getIM();
	im && im.off({
	  'onRosterMessage': this.receiveNewMessage,
	  'onMessageStatusChanged': this.onMessageStatusChanged,
	  'onSendingMessageStatusChanged': this.onSendingMessageStatusChanged,
	});
  },
  onLoad: function (options) {
    this.setData({
      navHeight: getApp().getNavHeight(),
	  showing: false
    });
    
    let {
      uid,
      nick = ''
    } = options;
    this.setData({
      uid: uid - 0
    });
	
	const im = getApp().getIM();
    if(im){
	  const messages = im.rosterManage.getRosterMessageByRid(uid - 0);
	  this.appendMessage({
	    messages
	  });
	  setTimeout(() => {
	    this.scroll();
	  }, 500);
	  im.on({
		'onRosterMessage': this.receiveNewMessage,
		'onMessageStatusChanged': this.onMessageStatusChanged,
		'onSendingMessageStatusChanged': this.onSendingMessageStatusChanged,
	  });
	  im.rosterManage.readRosterMessage(this.uid);
	}
    
    /** 设置title */

    const umaps = im.rosterManage.getAllRosterDetail();
    let fromUserObj = umaps[uid] || {};
    if (uid == 0) fromUserObj.username = '系统通知';
    this.setData({
      stitle: nick || fromUserObj.nick_name || fromUserObj.username || ''
    });
    const wh = wx.getSystemInfoSync().windowHeight - this.navHeight - 70;
    this.setData({
      wh
    });
  },
  methods: {
	onMessageStatusChanged: ({mid}) => {
	  console.log("Message status changed, mid: ", mid);
	},
	onSendingMessageStatusChanged: ({status, mid}) => {
	  console.log("Sending Message status changed to ", status," mid: ", mid);
	},
	
    appendMessage: function (data) {
      const newMessages = data.messages || [];
      const isHistory = data.history;
      const uid = getApp().getIM().userManage.getUid();
      const oldMessages = this.messages || [];
      // console.log("APPEND message new: ", newMessages);
      newMessages.forEach(meta => {
        isHistory && (meta.h = true); /////////////////////

        const {
          from,
          to
        } = meta;
        let saveUid = from == uid ? to : from;
        if (saveUid == '') saveUid = 0; // empty from means system message

        if (saveUid + '' !== this.uid + '') {
          return; // rosterchat, 必须有一个id是 sid
        }

        if (oldMessages.length > 0) {
          const firstItem = oldMessages[0];
          const lastItem = oldMessages[oldMessages.length - 1];
          const compFirst = toLong(meta.id).comp(toLong(firstItem.id || 0));
          const compLast = toLong(meta.id).comp(toLong(lastItem.id || 0));

          if (compFirst === -1) {
            // 比第一个小
            oldMessages.unshift(meta);
          } else if (compLast === 1) {
            oldMessages.push(meta);
          } else {
            let index = -1;

            for (var i = 0; i < oldMessages.length - 2; i += 1) {
              const compCurr = toLong(meta.id).comp(toLong(oldMessages[i].id));
              const compNext = toLong(meta.id).comp(toLong(oldMessages[i + 1].id));

              if (compCurr === 1 && compNext === -1) {
                index = i;
              }
            }

            if (index > -1) {
              oldMessages.splice(index, 0, meta); // 插入到这里
            }
          }
        } else {
          // 数组为空
          oldMessages.push(meta);
        }
      });
      this.setData({
        messages: [].concat(oldMessages)
      });
    },

    receiveNewMessage(message) {
	  const im = getApp().getIM();
	  if(!im) return;
	  
      const uid = im.userManage.getUid();
      const to = message.to;
      const from = message.from;
      const pid = this.uid;

      if ( uid == to && from == pid || uid == from && to == pid) {
        if (!this.checkTyping(message)) {
          const smessages = im.rosterManage.getRosterMessageByRid(this.uid - 0);
          this.appendMessage({
            messages: smessages
          });
          this.scroll();
        }
      }

      if (this.showing) {
        im.rosterManage.readRosterMessage(this.uid);
      }
    },

    checkTyping(message) {
      const {
        ext = {}
      } = message;

      if (typeof ext.input_status !== "undefined") {
        let status = ext.input_status;

        if (status == "nothing") {// this.header.querySelector(".typing").style.display = "none";
        } else {// this.header.querySelector(".typing").style.display = "inline";
            // this.header.querySelector(".typing").innerHTML = status + "...";
          }

        return true;
      }

      return false;
    },

    scroll() {
      const scrolltop = this.scrolltop + this.messages.length * 1000;
      this.setData({
        scrolltop
      });
    },

    sendMessageHandler() {
      const content = this.inputValue;

      if (content) {
        getApp().getIM().sysManage.sendRosterMessage({
          content,
          uid: this.uid,
		  // ext: "自定义消息字段",
        });
        setTimeout(() => {
          this.setData({
            inputValue: ''
          });
        }, 400);
      }
    },

    inputChangeHandler(evt) {
      const inputValue = evt.detail && evt.detail.value || '';
      this.setData({
        inputValue
      });
    },

    backClick() {
	  if( getCurrentPages().length > 1 ){
		wx.navigateBack();  
	  }else{
		wx.switchTab({
		  url: '/pages/contact/index'
		});  
	  }
    },
	
	deleteConversation() {
	  const also_delete_other_devices = true;
	  getApp().getIM().sysManage.deleteConversation(this.uid, also_delete_other_devices);
	  this.backClick();
	},

    voiceHandler() {
      this.setData({
        showvoice: !this.showvoice
      });
    },

    startRecord() {
      wx.authorize({
        scope: "scope.record",
        success: function () {
          console.log("录音授权成功");
        },
        fail: function () {
          console.log("录音授权失败");
        }
      });
      var that = this;
      const recorderManager = wx.getRecorderManager();
      recorderManager.onError(res => {
        console.log("录音错误: ", res);
      });
      recorderManager.onStop(function (res) {
        that.setData({
          recordFile: res.tempFilePath,
          duration: Math.ceil((new Date().getTime() - that.startTime) / 1000)
        });
        that.preupload(that.recordFile);
        console.log("录音完成: ", that.recordFile);
      });
      const options = {
        sampleRate: 44100,
        numberOfChannels: 1,
        encodeBitRate: 192000,
        format: 'mp3'
      };
      recorderManager.start(options);
      this.setData({
        startTime: new Date().getTime(),
        recordTxt: '录音中'
      });
      const timer = setTimeout(() => {
        this.stopRecord();
      }, 30000);
      this.setData({
        timer
      });
    },

    stopRecord() {
      if (this.timer) {
        clearTimeout(this.timer);
        this.setData({
          timer: null
        });
      }

      const recorderManager = wx.getRecorderManager();
      recorderManager.stop();
      this.setData({
        recordTxt: '按下录音'
      });
    },

    preupload(path) {
      const that = this;
      getApp().getIM().sysManage.asyncGetFileUploadChatFileUrl({
        file_type: 104,
        to_id: this.uid,
        to_type: 1
      }).then(res => {
        that.uploadVoice(path, res);
      });
    },

    uploadVoice(path, param) {
      const that = this;
	  const im = getApp().getIM();
      const token = im ? im.userManage.getToken() : undefined;
      wx.uploadFile({
        url: param.upload_url,
        filePath: path,
        name: "file",
        //后台要绑定的名称
        header: {
          "Content-Type": "multipart/form-data",
          'access-token': token,
          'app_id': getApp().getAppid()
        },
        //参数绑定
        formData: {
          OSSAccessKeyId: param.oss_body_param.OSSAccessKeyId,
          policy: param.oss_body_param.policy,
          key: param.oss_body_param.key,
          signature: param.oss_body_param.signature,
          callback: param.oss_body_param.callback
        },
        success: function (res) {
          that.sendVoiceMessage(param.download_url);
        },
        fail: function (ress) {
          console.log("。。录音保存失败。。");
        }
      });
    },

    sendVoiceMessage(url) {
      const im = getApp().getIM();
      const fileInfo = {
        dName: 'file',
        url,
        duration: this.duration
      };
      im.sysManage.sendRosterMessage({
        type: 'audio',
        uid: this.uid,
        content: "",
        attachment: fileInfo
      });
    }

  }
};
</script>
<style>
@import "./index.css";
</style>