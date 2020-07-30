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
	    } else {
			// do nothing.
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
	
	addIMListeners(){
	  const im = this.getIM();
	  if( !im ) return;
	  
	  im.on({
          loginSuccess: this.onLoginSuccess,
          loginFail: this.onLoginFail,
          flooNotice: msg => {
			console.log("Floo Notice: " + msg);
			const { category, desc } = msg;
            switch( category ) {
			  case 'action': 
			    if( 'relogin' == desc ){
				  wx.showToast({ title: "请重新登录"});	
				}else{
				  console.log("Floo Notice: unknown action ", desc);	
				}
                break;
			  case 'loginMessage':
				  console.log("IM login message: ", desc);
				  break;
              default:
				console.log("Floo Notice: unknown category " + category);
			}
          },
          flooError: msg => {
            const { category, desc } = msg;
            switch( category ) {
              case 'USER_BANNED':
				wx.showToast({ title: '用户错误:' + desc });
                break;
			  case 'DNS_FAILED':
			    wx.showToast({ title: "DNS错误: 无法访问 " + desc });
				break;
              default:
                console.log("Floo Error: " + category + " " + desc);
            }
          }
	  });
	},
	
	removeIMListeners(){
	  const im = this.getIM();
	  if (im) {
		im.off('loginSuccess', this.onLoginSuccess);
	    im.off('loginerror', this.onLoginFail);
	  }	
	},
	
	onLoginSuccess() {
	  wx.hideLoading(); //FIXME: chanage tester account
	
	  const info = this.getLoginInfo();
	  const username = info ? info.username : "";
	
	  if ('wechat_test' === username) {
	    // tester ... go work list ...
	    wx.redirectTo({
	      url: '/pages/work/list/index'
	    });
	  } else {
	    wx.switchTab({
	      url: '/pages/contact/index'
	    });
	  }
	},
	
	onLoginFail(msg) {
	  wx.hideLoading();
	  wx.showToast({
	    title: '登录出错:' + msg
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