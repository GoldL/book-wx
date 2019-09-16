<template>
  <div>
    <top-swiper :list="swiperList"></top-swiper>
    <card :key='index' v-for='(book, index) in books' :book='book'></card>
    <p class='text-footer' v-if='!more'>
      没有更多数据
    </p>
  </div>
</template>

<script>
import { get } from 'utils/utils'
import { booklistUrl, swiperListUrl } from 'api/api'
import Card from 'components/Card'
import TopSwiper from 'components/TopSwiper'
export default {
  name: 'Books',
  components: {
    Card,
    TopSwiper
  },
  data () {
    return {
      page: 0,
      size: 10,
      books: [],
      more: true,
      swiperList: []
    }
  },
  methods: {
    async getList (init) {
      if (init) {
        this.page = 0
        this.more = true
      }
      wx.showNavigationBarLoading()
      const data = await get(booklistUrl, { page: this.page, size: this.size })
      if (data.list.length < this.size && this.page > 0) {
        this.more = false
      }
      if (init) {
        this.books = data.list
        wx.stopPullDownRefresh()
      } else {
        // 上拉加载更多
        this.books = this.books.concat(data.list)
      }
      wx.hideNavigationBarLoading()
    },
    async getSwiperList () {
      const data = await get(swiperListUrl)
      this.swiperList = data.list
    }
  },
  onPullDownRefresh () {
    this.getList(true)
  },
  onReachBottom () {
    if (!this.more) {
      return
    }
    this.page++
    this.getList()
  },
  mounted () {
    this.getList(true)
    this.getSwiperList()
  }
}
</script>
