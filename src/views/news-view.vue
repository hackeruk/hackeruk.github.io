<template>
  <div class="news-view" v-class="loading:!items.length">
    <!-- item list -->
    <item v-repeat="item:items" page="{{displayPage}}" track-by="id"></item>
    <!-- navigation -->
    <div class="nav" v-show="items.length > 0">
      <a v-if="params.page > 1" href="#/news/{{params.page - 1}}">&lt; prev</a>
      <a v-if="params.page < 4" href="#/news/{{params.page + 1}}">more...</a>
    </div>
  </div>
</template>

<script>
var store = require('../store')

module.exports = {
  replace: true,
  props: ['params'],
  data: function () {
    return {
      params: {
        page: 1
      },
      displayPage: 1,
      items: []
    }
  },
  watch: {
    'params.page': function () {
      this.update(true)
    }
  },
  compiled: function () {
    this.update()
    store.on('update', this.update)
  },
  destroyed: function () {
    store.removeListener('update', this.update)
  },
  components: {
    item: require('../components/item.vue')
  },
  methods: {
    update: function (switchingPage) {
      store.fetchItemsByPage(this.params.page, function (items) {
        this.items = items
        this.displayPage = this.params.page
        if (switchingPage) {
          window.scrollTo(0, 0)
        }
      }.bind(this))
    }
  }
}
</script>

<style lang="stylus">
.news-view
  padding-left 5px
  padding-right 15px
  &.loading:before
    content "Loading..."
    position absolute
    top 16px
    left 20px
  .nav
    padding 10px 10px 10px 40px
    margin-top 10px
    border-top 2px solid #f60
    a
      margin-right 10px
      &:hover
        text-decoration underline
</style>
