<template>
  <div id="app">
    <news-page></news-page>
  </div>
</template>

<script>
import NewsPage from './components/NewsPage'

export default {
  name: 'app',
  components: {
    NewsPage
  }
}
</script>

<style lang="stylus">
  @import url('https://fonts.googleapis.com/css?family=Open+Sans:300,400,700&subset=cyrillic');

*
  margin 0
  padding 0
  font-family 'Open Sans', sans-serif
  scroll-behavior smooth

#app
  background-color #f2f2f2
</style>
