<template>
  <div id="comments">
    <div class="grid grid-centered" style="max-width: 660px;padding: 0px 20px;margin-top: 80px">
      <div class="grid-cell" id="grid-cell">
        <div id="header_info">
          <!-- 顶部标题与分类区块 -->
          <nav class="header-nav reveal">
            <div class="cate-nav">
              <div>
                <nuxt-link to="/" class="top1 header-logo cate-name">{{ $t('lang.comments.title') }}</nuxt-link>
              </div>
              <div>
                <nuxt-link to="/">
                  <b-button variant="primary" class="cate-back">
                    <i class="ri-arrow-left-line"></i>
                    {{ $t('lang.cate.backHome') }}
                  </b-button>
                </nuxt-link>
              </div>
            </div>
            <p class="top2 lead archive-p">{{ $t('lang.comments.des') }}</p>
          </nav>
          <topInsideCate :loading="false" />
          <!-- 顶部标题与分类区块 -->
        </div>

        <ul class="article-list">
          <!-- 加载骨架图 -->
          <li class="article-list-item reveal index-post-list bottom" v-if="loading">
            <div class="skeleton">
              <div class="skeleton-head"></div>
              <div class="skeleton-body">
                <div class="skeleton-title"></div>
                <div class="skeleton-content"></div>
              </div>
            </div>
          </li>
          <!-- 加载骨架图 -->
          <li
            v-for="(post,index) in posts"
            class="article-list-item reveal index-post-list"
            :key="'Comment' + post.id"
          >
            <div>
              <em
                style="color: rgb(153, 153, 153);font-style: normal;padding: 3px 7px;border-radius: 4px;background: #f1f2f3;"
              >{{ post.nick }}</em>
              <em
                style="color: rgb(153, 153, 153);font-style: normal;padding: 3px 7px;border-radius: 4px;background: #f1f2f3;margin-left: 5px;"
              >{{ post.email }}</em>
            </div>
            <nuxt-link
              :to="post.page_key == '249' ? '/page/249' : '/post/'+post.page_key"
              style="text-decoration:none"
            >
              <div class="content-c" v-html="$md.render(post.content)"></div>
            </nuxt-link>
            <div class="archive-footer">
              <em>{{ post.date }}</em>
              <em>
                <a
                  :href="post.link.length ? post.link : '/page/249'"
                >{{ $t('lang.comments.viewLink') }}</a>
              </em>
            </div>
          </li>
          <div class="music-view-more" v-if="!loading">
            <a
              href="#"
            >{{ $t('lang.comments.total') }} {{ posts.length }} {{ $t('lang.comments.line') }}</a>
          </div>
        </ul>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { Component, Vue } from 'nuxt-property-decorator'

// import header-top-inside
import topInsideCate from '@/components/topInsideCate.vue'

@Component({
  components: {
    topInsideCate
  }
})
export default class Comments extends Vue {

  flag: boolean = false
  posts: any[] = []
  loading: boolean = true
  errored: boolean = true

  head() {
    return {
      title: 'TonyHe - 评论列表'
    }
  }

  mounted() {
    //获取文章列表
    this.$axios
      .get(
        'https://blog.ouorz.com/wp-content/themes/peg/com/data/comments.data.json?rand=' +
          Math.random() * 1000
      )
      .then((response: { data: any }) => {
        this.posts = response.data.reverse()
      })
      .catch((): void => {
        this.errored = false
      })
      .then((): void => {
        this.loading = false
      })
  }

}
</script>