<template>
  <div class="container">
    <comment-list v-if='userinfo.openId' type="user" :comments="comments"></comment-list>
    <div v-if='userinfo.openId'>
      <div class="page-title">我的图书</div>
      <card v-for='book in books' :key='book.id' :book='book'></card>
      <div v-if='!books.length'>暂时还没添加过书，快去添加几本把</div>
    </div>
  </div>
</template>

<script>
import { get } from 'utils/utils'
import { commentlistUrl, booklistUrl } from 'api/api'
import CommentList from 'components/CommentList'
import Card from 'components/Card'
export default {
  components: {
    CommentList,
    Card
  },
  data () {
    return {
      comments: [],
      books: [],
      userinfo: {}
    }
  },
  methods: {
    init () {
      wx.showNavigationBarLoading()
      this.getComments()
      this.getBooks()
      wx.hideNavigationBarLoading()
    },
    async getComments () {
      const comments = await get(commentlistUrl, {
        openid: this.userinfo.openId
      })
      this.comments = comments.list || []
    },
    async getBooks () {
      const books = await get(booklistUrl, {
        openid: this.userinfo.openId
      })
      this.books = books.list
    }
  },
  onPullDownRefresh () {
    this.init()
    wx.stopPullDownRefresh()
  },
  onShow () {
    if (!this.userinfo.openId) {
      let userinfo = wx.getStorageSync('userinfo')
      if (userinfo) {
        this.userinfo = userinfo
        this.init()
      }
    }
  }

}
</script>
