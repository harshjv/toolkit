<template>
  <div class="gallery">
    <a-base-styles />
    <div class="gallery__sidebar">
      <gallery-sidebar :pages='pages' @openPage='handleOpenPage' />
    </div>
    <div class="gallery__content">
      <component :is="page.comp" />
    </div>
  </div>
</template>

<script>
  import Vue from 'vue'
  import { aBaseStyles } from 'toolkit'
  import createHistory from 'history/createBrowserHistory'
  import gallerySidebar from 'comps/gallery-sidebar.vue'

  import home from 'pages/home.vue'
  import button from 'pages/button.vue'

  const pages = [
    [home, 'Aragon Toolkit', '/'],
    [button, 'Button', '/button']
  ].map(p => ({
    comp: p[0],
    name: p[1],
    path: p[2]
  }))

  export default {
    components: {
      gallerySidebar,
      aBaseStyles,
    },
    data() {
      return {
        pages,
        page: pages[0]
      }
    },
    methods: {
      handleOpenPage(page) {
        this.history.push(page, {})
      },
      handleLocationUpdate(location) {
        const match = location.pathname.match(/^(\/[a-z]*)\/?$/)
        if (!match || !match[1]) {
          return
        }
        const page = pages.find(page => page.path === match[1])
        if (page) {
          console.log('NEW PAGE', page)
          this.page = page
        }
      },
    },
    created() {
      this.history = createHistory()
      this.unlistenHistory = this.history.listen(this.handleLocationUpdate)
      this.handleLocationUpdate(this.history.location, true)
    },
    destroyed() {
      this.unlistenHistory()
    }
  }
</script>

<style scoped>
  .gallery {
    display: flex;
    height: 100%;
  }
  .gallery__sidebar {
    width: 200px;
    padding-right: 20px;
    margin-right: 20px;
  }
  .gallery__content {
    padding: 20px;
  }
  .gallery__content .title {
    margin-bottom: 40px;
  }
</style>
