<template>
  <div class="news-page">
    <div class="news-page__number">{{number}}</div>
    <div class="pagination">
      <div class="pagination__prev" v-if="number > 1" @click="getPage(number - 1)">prev</div>
      <div class="pagination__next" @click="getPage(number + 1)">next</div>
    </div>
    <transition name="fade" mode="out-in">
      <div class="loading-circle" v-if="loading" key="loading">
        <svg xmlns="http://www.w3.org/2000/svg" version="1" width="120" height="120" viewBox="0 0 128 128"><g>
          <path d="M70.48 35.8l-6.16-6.24-6.16 6.26L64.32.88z"/><path fill="#e5e5e5" fill-opacity=".1" d="M83.712 42.818l-2.215-8.484-8.465 2.341L95.837 9.496zM91.662 55.512l2.324-8.455-8.501-2.205 33.339-12.135zM92.2 70.48l6.24-6.16-6.26-6.16 34.94 6.16zM85.182 83.712l8.484-2.215-2.341-8.465 27.179 22.805zM72.488 91.662l8.455 2.324 2.205-8.501 12.135 33.339zM57.52 92.2l6.16 6.24 6.16-6.26-6.16 34.94zM44.288 85.182l2.215 8.484 8.465-2.341-22.805 27.179z"/><path fill="#ccc" fill-opacity=".2" d="M36.338 72.488l-2.324 8.455 8.501 2.205L9.176 95.283z"/><path fill="#999" fill-opacity=".4" d="M35.8 57.52l-6.24 6.16 6.26 6.16L.88 63.68z"/><path fill="#666" fill-opacity=".6" d="M42.818 44.288l-8.484 2.215 2.341 8.465L9.496 32.163z"/><path fill="#333" fill-opacity=".8" d="M55.512 36.338l-8.455-2.324-2.205 8.501L32.717 9.176z"/><animateTransform attributeName="transform" type="rotate" values="0 64 64;30 64 64;60 64 64;90 64 64;120 64 64;150 64 64;180 64 64;210 64 64;240 64 64;270 64 64;300 64 64;330 64 64" calcMode="discrete" dur="960ms" repeatCount="indefinite"/></g>
        </svg>
      </div>
      <div class="loading-circle" v-else-if="error" key="error">
        ОШИБКА ЗАГРУЗКИ
      </div>
      <div class="news-list" v-else key="list">
        <a class="news-item" v-for="child in children" :key="child.data.name" target="_blank" :href="'http://reddit.com' + child.data.permalink">
            <img v-if="child.data.thumbnail && child.data.thumbnail.substr(0,4) === 'http'" :src="child.data.thumbnail" alt="" class="news-item__pic"/>
          <div class="news-item__no-pic" v-else>no img</div>
            <div class="news-item__title" v-html="child.data.title"></div>
        </a>
      </div>
    </transition>


  </div>
</template>

<script>
  import axios from 'axios'

  export default {
    name: 'news-page',
    data: () => ({
      info: {},
      number: 1,
      loading: true,
      error: false,
      show:true
    }),
    computed: {
      after () {
        return this.children.length ? `after=${this.children[this.children.length -1].data.name}` : ''
      },
      before () {
        return this.children.length ? `before=${this.children[0].data.name}` : ''
      },
      children () {
        return this.info.children || []
      }
    },
    mounted() {
      this.getPage()
    },
    methods: {
      getPage (number = 1) {
        let position = number > this.number ? this.after : this.before

        this.loading = true
        this.error = false

        axios
          .get(`https://www.reddit.com/r/all/.json?${position}`)
          .then(response => {
            this.info = response.data.data
            this.number = number
            this.loading = false
            window.scrollTo(0,0)
          })
          .catch(() => {
            this.error = true
            this.loading = false
          })
      }
    }
  }
</script>

<style lang="stylus">
  .news-page
    &__number
      text-align center
      margin-bottom 70px
      padding-top 20px

  .news-list
    margin 30px

  .news-item
    margin 10px
    padding 10px
    background-color white
    min-height 140px
    text-decoration none
    display block
    color #d44b38
    transition opacity.2s ease-in

    &:hover
      opacity .8

    &__title
      font-size 18px

    &__no-pic
    &__pic
      float left
      margin-right 20px
      width 140px

    &__no-pic
      height 140px
      background #f2f2f2
      text-align center
      text-transform uppercase
      letter-spacing 1px
      color #999
      line-height @height
      font-weight 300

  .loading-circle
    margin-top 300px
    text-align center
    height 100vh

  .pagination
    position fixed
    top 20px
    overflow hidden
    width 100%
    z-index 1
  .pagination__prev
    float left
  .pagination__next
    float right


  .pagination__next, .pagination__prev
    color #fff
    background rgb(212,75,56)
    padding .7em 1.5em
    outline none
    cursor pointer
    width 120px

  .pagination__next, .pagination__prev
    &:hover
      background rgb(232,95,76)
  .pagination__next, .pagination__prev
    &:active
      background rgb(152,15,0)


  .fade-enter-active, .fade-leave-active
    transition opacity .5s

  .fade-enter, .fade-leave-to
    opacity 0
</style>
