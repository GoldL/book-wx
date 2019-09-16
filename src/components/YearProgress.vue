<template>
  <div class="progressbar">
    <progress :percent='percent' activeColor='#EA5A49'></progress>
    <p>{{year}}已经过去了{{days}}天，{{percent}}%</p>
  </div>
</template>

<script>
export default {
  computed: {
    year () {
      return new Date().getFullYear()
    },
    days () {
      // 设置今年的第一天
      let startDay = new Date()
      startDay.setMonth(0)
      startDay.setDate(1)
      let offset = new Date().getTime() - startDay.getTime()
      return parseInt(offset / 1000 / 60 / 60 / 24) + 1
    },
    percent () {
      return (this.days * 100 / this.getDayOfYear()).toFixed(1)
    }
  },
  methods: {
    getDayOfYear () {
      return this.isLeapYear() ? 366 : 365
    },
    isLeapYear () {
      const year = new Date().getFullYear()
      if (year % 4 === 0 || (year % 4 === 0 && year % 100 !== 0)) {
        return true
      }
      return false
    }
  }
}
</script>

<style lang="scss">
.progressbar {
  text-align: center;
  margin-top: 10px;
  margin-bottom: 40px;
  width: 100%;
  progress {
    margin-bottom: 10px;
  }
}
</style>