<template>
  <view>
    <view class="mar30_tb pad50_lr flex space-between">
			<view class="flex" @click="clickBuy('$12')">
				<image src="../static/images/notice-icon01.png" class="w310 h100"></image>
			</view>
			<view class="flex" @click="clickBuy('$11')">
				<image src="../static/images/notice-icon02.png" class="w310 h100"></image>
			</view>
		</view>
		<view class="bg-white">
			<UniList>
				<SwipeAction>
				<SwipeActionItem v-for="(item, index) in listData" :key="index">
					<template v-slot:right>
						<view class="flex flex-direction align-center bg_FA5251 flex-center pad40_lr" @click="swipeClick(item, index)">
							<image src="../static/images/notice-icon03.png" style="width:32rpx;height:36rpx"></image>
							<text class="white mar20_t f13 fw">删除</text>
						</view>
					</template>
				<!-- 显示圆形头像 -->
				<!-- 右侧带角标 -->
				<ListChat :to="`/customerIm/chatPage/index?name=${item.title}&id=${item.id}`" clickable :avatar-circle="true" :title="item.title" :avatar="item.headIcon" :note="item.note" :time="item.time" :badge-text="item.badge"></ListChat>
				</SwipeActionItem>
				</SwipeAction>
			</UniList>
		</view>
  </view>
</template>
<script>
import UniList from './../compontent/uni-list/uni-list'
import ListChat from './../compontent/uni-list-chat/uni-list-chat'
import SwipeAction from './../compontent/uni-swipe-action/uni-swipe-action'
import SwipeActionItem from './../compontent/uni-swipe-action-item/uni-swipe-action-item'
export default {
	components: {
		UniList,
		ListChat,
		SwipeAction,
		SwipeActionItem
	},
	data() {
    return {
			listData: [
				{
					id: '3233',
					headIcon: 'https://file.caoduyun.com/x_image_6c2dc8d8-d8e7-429b-8a04-c29961b874c0.jpg?imageView2/1/w/100',
					title: '张北县小娟牲畜服务部',
					note: '此条消息为最新消息内容最新消息内内容最新消息内',
					time: '09:23',
					badge: '99+'
				},
				{
					id: '323344',
					headIcon: 'https://file.caoduyun.com/x_image_6c2dc8d8-d8e7-429b-8a04-c29961b874c0.jpg?imageView2/1/w/100',
					title: '万新牲畜服务部',
					note: '此条消息为最新消息内容最新消息内内容最新消息内',
					time: '昨天',
					badge: '12'
				},
				{
					id: '323355',
					headIcon: 'https://file.caoduyun.com/x_image_6c2dc8d8-d8e7-429b-8a04-c29961b874c0.jpg?imageView2/1/w/100',
					title: '安平橡皮树服务部',
					note: '此条消息为最新消息内容最新消息内内容最新消息内',
					time: '星期一',
					badge: '2'
				},
				{
					id: '323366',
					headIcon: 'https://file.caoduyun.com/x_image_6c2dc8d8-d8e7-429b-8a04-c29961b874c0.jpg?imageView2/1/w/100',
					title: '马拉加牧草服务部',
					note: '此条消息为最新消息内容最新消息内内容最新消息内',
					time: '2022-05-14',
					badge: '20'
				}
			],
			loadMoreText: "加载中...",
      pageParam: {
				totalPage: 0,
        pageNum: 1,
        pageSize: 10
      },
		}
	},
	onLoad() {
  },
	onReachBottom() {
    console.log("onReachBottom");
    if (this.pageParam.totalPage <= this.pageParam.pageNum) {
      return;
    }
    this.pageParam.pageNum++;
    this.showLoadMore = true;
    this.loadData();
  },
  onPullDownRefresh() {
    // 下拉刷新
    uni.stopPullDownRefresh();
    this.pageParam.pageNum = 1;
    this.listData = [];
    this.loadData();
  },
  methods: {
		swipeClick(e, index) {
				let {
					content
				} = e;
					uni.showModal({
						title: '提示',
						content: '是否删除',
						success: res => {
							if (res.confirm) {
								//this.swipeList.splice(index, 1);
							} else if (res.cancel) {
								console.log('用户点击取消');
							}
						}
					});
		}
	}
}
</script>
<style lang="scss" scoped>
@import "../static/css/global.scss";
.bg_FA5251{
	background-color: #FA5251
}
</style>