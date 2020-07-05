<script>
import flooim from './im/floo-2.0.0.uniapp';

export default {
  onLaunch: function () {
    wx.showShareMenu();
  },
  
  onLoad() {
	this.setNavPosition();  
  },

  onShow() {
    this.ensureIMLogin();
  },

  onHide() {
    if (this.getIM().disConnect) {
      console.log('disconnect onHide ....');
      this.getIM().disConnect();
    }
  },

  globalData: {
    im: {},
	navTop: 24,
    navHeight: 60,
    dnsServer: "https://dns.maximtop.com/app_dns",
    appid: "welovemaxim",
    ws: true,
    autoLogin: true
  },
  
  methods: {
	ensureIMLogin() {
	  if ( !(this.getIM() && this.getIM().isLogin) ){
	    this.initSDK();
	  }
	
	  const im = this.getIM();
	
	  if (im && im.login) {
	    const loginInfo = this.getLoginInfo();
	
	    if (loginInfo && loginInfo.username) {
	      im.login({
	        //TODO: change name to username
	        name: loginInfo.username,
	        password: loginInfo.password
	      });
	    } else if (loginInfo && loginInfo.user_id) {
	      im.idLogin({
	        user_id: loginInfo.user_id,
	        password: loginInfo.password
	      });
	    }
	  }
	},
	
	initSDK() {
	  const appid = this.getAppid();
	  const dnsServer = this.globalData.dnsServer;
	  const ws = this.globalData.ws;
	  const autoLogin = this.globalData.autoLogin;
	  console.log("Init flooim for ", appid);
	  const config = {
	    autoLogin,
	    dnsServer,
	    appid,
	    ws
	  };
	  const im = flooim(config);
	
	  if (im) {
	    this.globalData.im = im;
	  }
	},
	
	getIM() {
	  return this.globalData.im;
	},
	
	getAppid() {
	  return this.globalData.appid;
	},
	
	setupIM(appid) {
	  console.log("Change appid to ", appid);
	  this.globalData.appid = appid;
	  if( this.getIM() && this.getIM().logout ){
		this.getIM().logout();
	  }
	  
	  this.globalData.im = {};
	  this.ensureIMLogin();
	},
	
	isIMLogin() {
	  const im = this.getIM();
	  return im && im.isLogin && im.isLogin();
	},
	
	saveLoginInfo(info) {
	  wx.setStorageSync('maxim_logininfo', info);
	},
	
	getLoginInfo() {
	  return wx.getStorageSync('maxim_logininfo') || {};
	},
	
	imLogout() {
	  const info = this.getLoginInfo();
	  console.log("IM logout: ", info);
	  this.getIM().logout();
	  wx.removeStorageSync('maxim_logininfo');
	  wx.reLaunch({
	    url: '../account/login/index'
	  });
	},
	
	getNavTop() {
	  // this.setNavPosition();
	  return this.globalData.navTop;
	},
	
	getNavHeight() {
	  // this.setNavPosition();
	  return this.globalData.navHeight;
	},
	setNavPosition() {
	  let menuButtonObject = wx.getMenuButtonBoundingClientRect();
	  if( !menuButtonObject ) return;
	  
	  wx.getSystemInfo({
	    success: res => {
	      let statusBarHeight = res.statusBarHeight;
	      this.globalData.navTop = menuButtonObject.top; //胶囊按钮与顶部的距离
	      this.globalData.navHeight = statusBarHeight + menuButtonObject.height 
			+ (menuButtonObject.top - statusBarHeight) * 2; //导航高度
			
		  console.log("NavHeight: " + this.globalData.navHeight + " navTop: "+ this.globalData.navTop);
	    },
		fail: err => {
		  console.log(err);
		}
	  });
	}
  }
};
</script>
<style>
@import "./app.css";
</style>