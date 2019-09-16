<template>
  <div class="container">
    <button
      class="btn mr-100"
      open-type="getUserInfo"
      v-if="canIUse && !isSetting"
      @getuserinfo="bindGetUserInfo"
    >授权登录</button>
    <div v-else class="userinfo" @click="login">
      <img :src="userInfo.avatarUrl" />
      <p>{{userInfo.nickName}}</p>
    </div>
    <year-progress></year-progress>
    <button v-if="userInfo.openId" @click="scanBook" class="btn">添加图书</button>
  </div>
</template>

<script>
import { post, showSuccess, showModal } from 'utils/utils'
import qcloud from 'wafer2-client-sdk'
import { addBookUrl, loginUrl } from 'api/api'
import YearProgress from 'components/YearProgress'
export default {
  name: 'Me',
  components: {
    YearProgress
  },
  data () {
    return {
      isSetting: true,
      canIUse: wx.canIUse('button.open-type.getUserInfo'),
      userInfo: {
        avatarUrl: '/static/img/unlogin.png',
        nickName: '点击登录'
      }
    }
  },
  methods: {
    async addBook (isbn) { // 添加图书
      const res = await post(addBookUrl, {
        isbn,
        openid: this.userInfo.openId
      })
      showModal('添加成功', `《${res.title}》添加成功`)
    },
    scanBook () { // 识别条形码
      wx.scanCode({
        success: res => {
          if (res.result) {
            this.addBook(res.result)
          }
        }
      })
    },
    bindGetUserInfo (e) { // 授权登录回调
      if (e.target.signature) {
        this.isSetting = true
        this.login()
      }
    },
    init () { // 查看是否授权
      wx.getSetting({
        success: res => {
          if (!res.authSetting['scope.userInfo']) {
            this.isSetting = false
            return
          }
          this.isSetting = true
          let userInfo = wx.getStorageSync('userinfo')
          if (userInfo) {
            this.userInfo = userInfo
          }
        }
      })
    },
    login () { // 登录
      let user = wx.getStorageSync('userinfo')
      if (!user) {
        qcloud.setLoginUrl(loginUrl)
        qcloud.login({
          success: userInfo => {
            try {
              showSuccess('登陆成功')
              wx.setStorageSync('userinfo', userInfo)
              this.userInfo = userInfo
            } catch (e) {
            }
          },
          fail: err => {
            console.log('登录失败', err)
          }
        })
      }
    }
  },
  onShow () {
    this.init()
  }
}
</script>

<style lang="scss">
.container {
  padding: 30rpx;
  .mr-100 {
    margin-top: 200rpx;
  }
  .userinfo {
    margin-top: 100rpx;
    text-align: center;
    img {
      width: 150rpx;
      height: 150rpx;
      margin: 20rpx;
      border-radius: 50%;
    }
  }
}
</style>
