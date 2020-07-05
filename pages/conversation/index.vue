<template>
<view>
<snav>
  <text class="atitle">美信拓扑</text>
</snav>
<view class="container" :style="'padding-top:' + navHeight + 'px'">
  <view v-for="(conversation, index) in conversationList" :key="index" @tap="touchConversation" :data-type="conversation.type" :data-sid="conversation.sid" class="item">
    <view v-if="conversation.unread != 0" class="unread_number">
      <text>{{conversation.unread}}</text>
    </view>

    <view v-if="conversation.sid == 0">
      <image :src="system_avatar.avatar" class="avatar"></image>
      <view class="name">
        <text>{{system_avatar.name}}</text>
      </view>
    </view>
    <view v-else>
      <image :src="conversation.avatar" class="avatar"></image>
      <view class="name">
        <text>{{conversation.name}}</text>
      </view>
    </view>
    <view class="last_msg">
      <text>{{conversation.content}}</text>
    </view>
  </view>
</view>
</view>
</template>

<script>

export default {
  data() {
    return {
      conversationList: [],
      navHeight: 0,
      system_avatar: {
        name: "系统通知",
        avatar: "/static/pages/image/tab/setting.png"
      }
    };
  },

  components: {},
  props: {},
  onShow: function () {
    const token = getApp().getIM().userManage.getToken();

    if (!token) {
      if (getApp().isLoginPage === false) {
        getApp().isLoginPage = true;
        wx.reLaunch({
          url: '../login/index'
        });
      }
    } else {
      this.getConversationList();
    }
  },
  onLoad: function () {
    this.setData({
      navHeight: getApp().getNavHeight()
    });
	
	const im = getApp().getIM();
	if (im) {
	  im.on("onRosterMessage", message => {
	    this.getConversationList();
	  });
	  im.on("onGroupMessage", message => {
	    this.getConversationList();
	  });
	  im.on("onRosterListUpdate", message => {
	    this.getConversationList();
	  });
	  
	  im.on("onDisconnect", () => {// im.userManage.deleteToken();
	    // if (getApp().isLoginPage === false) {
	    //   getApp().isLoginPage = true;
	    //   wx.reLaunch({
	    //     url: '../login/index',
	    //   })
	    // }
	  });
	}
  },
  methods: {
    touchConversation(e) {
      var id = e.currentTarget.dataset.sid;
      const type = e.currentTarget.dataset.type;

      if (type === 'roster') {
        wx.navigateTo({
          url: '../roster/index?uid=' + id
        });
      } else {
        wx.navigateTo({
          url: '../group/index?gid=' + id
        });
      }
    },

    getConversationList() {
      const im = getApp().getIM();
      const uid = im.userManage.getUid();
      let list = im.userManage.getConversationList();
      list = list.filter(x => x.id !== uid);
      const allGroupMap = im.groupManage.getAllGroupDetail();
      const allRosterMap = im.rosterManage.getAllRosterDetail() || {};
      const slist = list.map((item, index) => {
        let name;
        const id = item.id;
        const content = item.content;
        let avatar = '';
        const unreadCount = item.type == 'roster' ? im.rosterManage.getUnreadCount(id) : im.groupManage.getUnreadCount(id);
        const unread = unreadCount > 0 ? unreadCount : 0;

        if (item.type === 'roster') {
          //roster
          const sroster = allRosterMap[id] || {};
          name = sroster.nick_name || sroster.username || id;
          avatar = im.sysManage.getImage({
            avatar: sroster.avatar,
            sdefault: "/static/pages/image/r.png"
          });
        } else if (item.type === 'group') {
          //group
          const sgroup = allGroupMap[id] || {};
          name = sgroup.name || id;
          avatar = im.sysManage.getImage({
            avatar: sgroup.avatar,
            type: "group",
            sdefault: "/static/pages/image/g.png"
          });
        }

        return {
          type: item.type,
          index,
          name,
          content,
          avatar,
          unread,
          sid: id
        };
      });
      this.setData({
        conversationList: [].concat(slist)
      });
    }

  }
};
</script>
<style>
@import "./index.css";
</style>