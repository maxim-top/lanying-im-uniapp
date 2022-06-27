<template>
  <view class="padBot">
    <view>
      <chart-list />
    </view>
    <!--聊天工具-->
    <view class="chat-tool">
      <view>
        <view class="flex align-end space-between pad20_lr pad14_tb">
          <view class="flex align-center mar20_r h90"><image src="../static/images/chat-icon05.png" style="width:48rpx;height:48rpx"></image></view>
          <view class="flex flex-auto pad20_tb pad30_lr bg-white radius40"><textarea auto-height v-model="evaluateBody" class="w100m h100m minHeight" placeholder-class="gray9 f14"  placeholder="请输入聊天内容" maxlength="200"></textarea> </view>
          <view class="flex align-center mar20_l h90"><image src="../static/images/chat-icon06.png" style="width:48rpx;height:48rpx"></image></view>
        </view>
        <view class="pad50_lr pad40_tb border_t flex space-between">
          <view class="flex align-center flex-direction">
            <image src="../static/images/chat-icon04.png" style="width:116rpx;height:116rpx"></image>
						<text class="gray6 mar20_t f12 fw">相册</text>
          </view>
          <view class="flex align-center flex-direction">
            <image src="../static/images/chat-icon03.png" style="width:116rpx;height:116rpx"></image>
						<text class="gray6 mar20_t f12 fw">拍照</text>
          </view>
          <view class="flex align-center flex-direction">
            <image src="../static/images/chat-icon02.png" style="width:116rpx;height:116rpx"></image>
						<text class="gray6 mar20_t f12 fw">语音电话</text>
          </view>
          <view class="flex align-center flex-direction">
            <image src="../static/images/chat-icon01.png" style="width:116rpx;height:116rpx"></image>
						<text class="gray6 mar20_t f12 fw">拨打电话</text>
          </view>
        </view>
      </view>
    </view>
  </view>
</template>
<script>
import chartList from './../compontent/chart-list/chart-list'
	export default {
    components: {
		  chartList
	  },
		data() {
      return {
        mainData: ''
      }
		},
		onLoad(options) {
      uni.setNavigationBarTitle({
        title: options.name
      })
		},
    onShow() {
      console.log(222)
    },
	}
</script>

<style lang="scss" scoped>
@import "../static/css/global.scss";
page {
  background-color: #f2f2f2;
}
.chat-tool{
  background-color: #fafafa;
  position: fixed;
  left: 0;
  bottom: 0;
  right: 0;
}
.minHeight {
  line-height: 50rpx;
}
.padBot{
  padding-bottom: 380rpx;
}
</style>
