<template>
  <div>
    <book-info :info="info"></book-info>
    <comment-list :comments="comments"></comment-list>
    <div class="comment" v-if="showAdd">
      <textarea v-model='comment'
        class='textarea'
        :maxlength='100'
        placeholder='请输入图书短评'></textarea>
      <div class='location'>
        地理位置：
        <switch :checked="location" color='#EA5A49' @change="getGeo"></switch>
        <span class='text-primary'>{{location}}</span>
      </div>
      <div class='phone'>
        手机型号：
        <switch :checked="phone" color='#EA5A49' @change="getPhone"></switch>
        <span class='text-primary'>{{phone}}</span>
      </div>
      <button class="btn" @click='addComment'>
        评论
      </button>
    </div>
    <div v-else class='text-footer'>
      未登录或者已经评论过啦
    </div>
    <button open-type='share' class="btn">转发给好友</button>
  </div>
</template>
<script>
import { bookDetailUrl, baduMapUrl, addCommentUrl, commentlistUrl } from 'api/api'
// import { baiduMapAK } from 'api/config'
import { get, post, showModal } from 'utils/utils'
import BookInfo from 'components/BookInfo'
import CommentList from 'components/CommentList'
export default {
  name: 'Detail',
  components: {
    BookInfo,
    CommentList
  },
  data () {
    return {
      bookid: '',
      userinfo: {},
      info: {},
      comments: [],
      comment: '',
      location: '',
      phone: ''
    }
  },
  computed: {
    showAdd () {
      // 未登录
      if (!this.userinfo.openId) {
        return false
      }
      // 评论页面里查到有自己的openid
      if (this.comments.filter(comment => comment.openid === this.userinfo.openId).length) {
        return false
      }
      return true
    }
  },
  methods: {
    async getDetail () {
      const info = await get(bookDetailUrl, { id: this.bookid })
      wx.setNavigationBarTitle({ title: info.title })
      this.info = info
    },
    async getComments () {
      const comments = await get(commentlistUrl, {bookid: this.bookid})
      this.comments = comments.list || []
    },
    async addComment () {
      if (!this.comment) {
        return
      }
      const data = {
        openid: this.userinfo.openId,
        bookid: this.bookid,
        comment: this.comment,
        phone: this.phone,
        location: this.location
      }
      try {
        await post(addCommentUrl, data)
        this.comment = ''
        this.getComments()
      } catch (e) {
        showModal('失败', e.msg)
      }
    },
    getGeo (e) {
      if (e.target.value) {
        wx.getLocation({
          success: geo => {
            wx.request({
              url: baduMapUrl,
              data: {
                location: `${geo.latitude},${geo.longitude}`,
                output: 'json',
                src: 'webapp.baidu.openAPIdemo'
              },
              success: res => {
                if (res.data.status === 'OK') {
                  this.location = res.data.result.addressComponent.city
                } else {
                  this.location = '未知地点'
                }
              }
            })
          }
        })
      }
    },
    getPhone (e) {
      if (e.target.value) {
        const phoneInfo = wx.getSystemInfoSync()
        this.phone = phoneInfo.model
      } else {
        this.phone = ''
      }
    }
  },
  mounted () {
    this.bookid = this.$root.$mp.query.id
    this.getDetail()
    this.getComments()
    const userinfo = wx.getStorageSync('userinfo')
    if (userinfo) {
      this.userinfo = userinfo
    }
  }
}
</script>

<style lang='scss'>
.comment {
  margin-top: 10px;
  .textarea {
    width: 730rpx;
    height: 200rpx;
    background: #eee;
    padding: 10rpx;
  }
  .location {
    margin-top: 10px;
    padding: 5px 10px;
  }
  .phone {
    margin-top: 10px;
    padding: 5px 10px;
  }
}
</style>
