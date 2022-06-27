<template>
  <view class="border_b pad30_tb pad_bottom">
    <!-- 聊天内容 -->
    <scroll-view
      class="chat"
      scroll-y="true"
      scroll-with-animation="true"
      :scroll-into-view="scrollToView"
      @scrolltoupper="scrollTop"
    >
      <view class="chat-main" :style="{ paddingBottom: inputh + 'px' }">
        <view class="chat-ls" v-for="(item, index) in msg" :key="index" :id="'msg' + index">
          <view class="chat-time" v-if="item.createTime != ''">2022-06-23 16:04:07</view>
          <view class="msg-m msg-left" v-if="item.sendName == friendName">
            <image
              class="user-img"
              src="https://file-tandian.nxin.com/x_image_81849323-10f2-4459-b5be-426fa69d064a.jpg?imageView2/1/w/128"
            ></image>
            <view class="message" v-if="item.TextType == 0">
              <!-- 文字 -->
              <view class="msg-text">{{ item.sendText }}</view>
            </view>
            <view class="message" v-if="item.TextType == 1" @click="previewImg(item.sendText)">
              <!-- 图像 -->
              <image :src="item.sendText" class="msg-img mar10_l" mode="widthFix"></image>
            </view>
            <view class="message" v-if="item.TextType == 2" @click="playVoice(item.sendText.voice)">
              <!-- 音频 -->
              <view class="msg-text voice">
                <image src="../../static/images/chat-icon08.png" class="voice-img mar10_r"></image>
                {{ item.sendText.time }}″
              </view>
            </view>
          </view>
          <view class="msg-m msg-right" v-if="item.sendName != friendName">
            <image class="user-img" src="https://file-tandian.nxin.com/icons/head.png"></image>
            <view class="message" v-if="item.TextType == 0">
              <view class="msg-text">{{ item.sendText }}</view>
            </view>
            <view class="message" v-if="item.TextType == 1" @click="previewImg(item.sendText)">
              <image :src="item.sendText" class="msg-img mar10_r" mode="widthFix"></image>
            </view>
            <view class="message" v-if="item.TextType == 2" @click="playVoice(item.sendText.voice)">
              <!-- 音频 -->
              <view class="msg-text voice">
                {{ item.sendText.time }}″
                <image src="../../static/images/chat-icon08.png" class="voice-img"></image>
              </view>
            </view>
          </view>
        </view>
      </view>
    </scroll-view>
  </view>
</template>
<script>
//音频播放
const innerAudioContext = uni.createInnerAudioContext();
export default {
  props: {
    item: {
      type: Object,
      default: {}
    }
  },
  components: {},
  data() {
    return {
      unshiftmsg: [],
      friendName: 'xpq',
      msg: [
        {
          sendName: 'xpq',
          receviceName: '゛时光い',
          sendText: {
            address: '湖南省岳阳市湘阴县新世纪大道',
            latitude: 28.68925,
            longitude: 112.90917,
            name: '湘阴县政府(新世纪大道北)'
          },
          createTime: '2022-01-06 12:40:12',
          updateTime: null,
          chatmState: 1,
          TextType: 3
        },
        {
          sendName: '゛时光い',
          receviceName: 'xpq',
          sendText: {
            voice: '时光匆匆流过',
            time: 2 //秒
          },
          createTime: '2022-01-06 12:22:12',
          updateTime: null,
          chatmState: 1,
          TextType: 2
        },
        {
          sendName: 'xpq',
          receviceName: '゛时光い',
          sendText: {
            voice: '谢谢你',
            time: 60 //秒
          },
          createTime: '2022-01-06 12:00:12',
          updateTime: null,
          chatmState: 1,
          TextType: 2
        },
        {
          sendName: '゛时光い',
          receviceName: 'xpq',
          sendText: '这是第九条未读消息',
          createTime: '2022-01-03 12:22:12',
          updateTime: null,
          chatmState: 1,
          TextType: 0
        },
        {
          sendName: '゛时光い',
          receviceName: 'xpq',
          sendText: '这是第八条未读消息',
          createTime: '2022-01-02 12:22:07',
          updateTime: null,
          chatmState: 1,
          TextType: 0
        },
        {
          sendName: 'xpq',
          receviceName: 'xpq',
          sendText: '这是第七条未读消息',
          createTime: '2021-12-19 12:22:03',
          updateTime: null,
          chatmState: 1,
          TextType: 0
        },
        {
          sendName: '゛时光い',
          receviceName: 'xpq',
          sendText: '这是第六条未读消息',
          createTime: '2021-12-19 12:21:58',
          updateTime: null,
          chatmState: 1,
          TextType: 0
        },
        {
          sendName: '゛时光い',
          receviceName: 'xpq',
          sendText:
            'http://demo.rageframe.com/attachment/images/2021/11/18/image_1637224530_diIlZlmm.jpeg',
          createTime: '2021-12-19 12:21:54',
          updateTime: null,
          chatmState: 1,
          TextType: 1
        },
        {
          sendName: 'xpq',
          receviceName: '゛时光い',
          sendText:
            'http://demo2.rageframe.com/attachment/images/2021/09/01/image_1630483477_N03W37zs.jpg',
          createTime: '2021-12-19 12:21:48',
          updateTime: null,
          chatmState: 1,
          TextType: 1
        },
        {
          sendName: '゛时光い',
          receviceName: 'xpq',
          sendText: '这是第三条未读消息',
          createTime: '2021-12-19 12:21:42',
          updateTime: null,
          chatmState: 1,
          TextType: 0
        },
        {
          sendName: '゛时光い',
          receviceName: 'xpq',
          sendText: '这是第二条未读消息',
          createTime: '2021-12-19 12:21:33',
          updateTime: null,
          chatmState: 1,
          TextType: 0
        },
        {
          sendName: '゛时光い',
          receviceName: 'xpq',
          sendText:
            'http://demo2.rageframe.com/attachment/images/2021/09/01/image_1630483477_N03W37zs.jpg',
          createTime: '2021-12-19 11:02:18',
          updateTime: null,
          chatmState: 1,
          TextType: 1
        },
        {
          sendName: '゛时光い',
          receviceName: 'xpq',
          sendText: '爱你啊',
          createTime: '2021-12-18 20:37:03',
          updateTime: null,
          chatmState: 0,
          TextType: 0
        }
      ],
      imgMsg:[]
    };
  },
  mounted() {
    this.unshiftmsg.concat(this.msg);
  },
  methods: {
    clickItem(item) {
      this.$emit('clickItem', item);
    },
    scrollTop(){
      console.log('到顶了')
    },
    // 进行图片的预览
    previewImg(e) {
      // 预览图片
      uni.previewImage({
        current: 0,
        urls: [e],
        longPressActions: {
          itemList: ['发送给朋友', '保存图片', '收藏'],
          success: function(data) {
            console.log('选中了第' + (data.tapIndex + 1) + '个按钮,第' + (data.index + 1) + '张图片');
          },
          fail: function(err) {
            console.log(err.errMsg);
          }
        }
      });
    },
    //音频播放
    playVoice(e) {
      console.log(1111111, e)
      innerAudioContext.src = e;
      innerAudioContext.onPlay(() => {
        console.log('开始播放');
      });
    },
  }
};
</script>
<style lang="scss" scoped>
.chat {
  height: 100%;
  .chat-main {
    padding-left: 32rpx;
    padding-right: 32rpx;
    padding-top: 20rpx;
    // padding-bottom: 120rpx;  //获取动态高度
    display: flex;
    flex-direction: column;
  }
  .chat-ls {
    .chat-time {
      font-size: 24rpx;
      color: rgba(39, 40, 50, 0.3);
      line-height: 34rpx;
      padding: 10rpx 0rpx;
      text-align: center;
    }
    .msg-m {
      display: flex;
      padding: 20rpx 0;
      .user-img {
        flex: none;
        width: 80rpx;
        height: 80rpx;
        border-radius: 20rpx;
      }
      .message {
        flex: none;
        max-width: 480rpx;
      }
      .msg-text {
        font-size: 32rpx;
        color: rgba(39, 40, 50, 1);
        line-height: 44rpx;
        padding: 18rpx 24rpx;
      }
      .msg-img {
        max-width: 400rpx;
        border-radius: 20rpx;
      }
      .msg-map {
        background: #fff;
        width: 464rpx;
        height: 284rpx;
        overflow: hidden;
        .map-name {
          font-size: 32rpx;
          color: rgba(39, 40, 50, 1);
          line-height: 44rpx;
          padding: 18rpx 24rpx 0 24rpx;
          //下面四行是单行文字的样式
          display: -webkit-box;
          -webkit-box-orient: vertical;
          -webkit-line-clamp: 1;
          overflow: hidden;
        }
        .map-address {
          font-size: 24rpx;
          color: rgba(39, 40, 50, 0.4);
          padding: 0 24rpx;
          //下面四行是单行文字的样式
          display: -webkit-box;
          -webkit-box-orient: vertical;
          -webkit-line-clamp: 1;
          overflow: hidden;
        }
        .map {
          padding-top: 8rpx;
          width: 464rpx;
          height: 190rpx;
        }
      }
      .voice {
        // width: 200rpx;
        min-width: 100rpx;
        max-width: 400rpx;
      }
      .voice-img {
        width: 28rpx;
        height: 36rpx;
      }
    }
    .msg-left {
      flex-direction: row;
      .msg-text {
        margin-left: 16rpx;
        background-color: #fff;
        border-radius: 0rpx 20rpx 20rpx 20rpx;
      }
      .ms-img {
        margin-left: 16rpx;
      }
      .msh-map {
        margin-left: 16rpx;
        border-radius: 0rpx 20rpx 20rpx 20rpx;
      }
      .voice {
        text-align: right;
      }
      .voice-img {
        float: left;
        transform: rotate(180deg);
        width: 28rpx;
        height: 36rpx;
        padding-bottom: 4rpx;
      }
    }
    .msg-right {
      flex-direction: row-reverse;
      .msg-text {
        margin-right: 16rpx;
        background-color: rgba(255, 228, 49, 0.8);
        border-radius: 20rpx 0rpx 20rpx 20rpx;
      }
      .ms-img {
        margin-right: 16rpx;
      }
      .msh-map {
        margin-left: 16rpx;
        border-radius: 20rpx 0rpx 20rpx 20rpx;
      }
      .voice {
        text-align: left;
      }
      .voice-img {
        float: right;
        padding: 4rpx;
        width: 28rpx;
        height: 36rpx;
      }
    }
  }
}
</style>
