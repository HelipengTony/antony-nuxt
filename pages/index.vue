<template>
  <client-only>
    <div id="index">
      <div
        class="grid grid-centered"
        style="max-width: 660px;padding: 0px 20px;margin-top: 80px"
      >
        <div class="grid-cell" id="grid-cell">
          <!-- 顶部标题与分类区块 -->
          <template v-if="!loading_tages">
            <headerTop
              :loading_tages="loading_tages"
              :loading_cates="loading_cates"
              :tages="tages"
              :cookie="notice.visible"
            />
          </template>
          <template v-else>
            <headerTop
              :loading_tages="loading_tages"
              :loading_cates="loading_cates"
              :cookie="notice.visible"
            />
          </template>
          <!-- 顶部标题与分类区块 -->

          <ul class="article-list">
            <li
              :class="
                'article-list-item reveal index-post-list ' +
                  (post.sticky ? 'sticky-one' : '')
              "
              v-for="(post, index) in posts"
              :key="'indexPost' + post.id"
              :id="'div' + post.id"
            >
              <!-- 不包含特色图像文章 -->
              <template
                v-if="
                  post.post_img.url == false &&
                    post.post_categories[0].term_id !== 58 &&
                    !post.post_metas.status
                "
              >
                <div class="list-show-div">
                  <!-- 置顶文章提示 -->
                  <em
                    class="article-list-type1 sticky-one-tag"
                    v-if="post.sticky"
                  >
                    <i class="ri-arrow-up-circle-line"></i>
                    {{ $t("lang.index.atTop") }}
                  </em>
                  <!-- 置顶文章提示 -->
                  <em
                    v-if="post.post_categories[0].term_id === 7"
                    class="article-list-type1"
                    v-html="
                      '<b>' +
                        post.post_categories[0].name +
                        '</b>' +
                        ' | ' +
                        (post.post_metas.tag_name
                          ? post.post_metas.tag_name.toUpperCase()
                          : '技术')"></em>
                  <div v-else class="article-list-tags">
                    <nuxt-link
                      class="list-normal-tag"
                      style="color: rgba(255, 152, 0, 0.83) !important;"
                      :to="'/cate/' + post.post_categories[0].term_id"
                      v-html="post.post_categories[0].name"
                    ></nuxt-link>
                    <template v-if="!post.post_tags.length">
                      <a style="margin-left: 5px;">{{
                        $t("lang.index.noneTag")
                      }}</a>
                    </template>
                    <template>
                      <a
                        v-for="(tag, index) in post.post_tags.slice(0, 2)"
                        v-html="tag.name"
                        :key="'indexPostTag' + tag.id"
                        style="margin-left: 5px;"
                        :href="'/tag/' + tag.id"
                      ></a>
                    </template>
                  </div>
                  <button
                    type="button"
                    class="list-show-btn"
                    @click="
                      post.id == previewPostOpened
                        ? closePreview(post.id)
                        : preview(post.id)
                    "
                    :id="'btn' + post.id"
                  >
                    {{ $t("lang.index.quickView") }}
                  </button>
                </div>

                <nuxt-link
                  :to="'/post/' + post.id"
                  style="text-decoration: none;"
                >
                  <h5 v-html="post.title.rendered"></h5>
                </nuxt-link>

                <!-- 文章速览 -->
                <!-- previewClose 已关闭的速览显示描述内容,previewPost 准备开始速览显示加载圆圈,previewPostOpened 开启的速览显示全文内容 -->
                <template v-if="previewClose !== post.id">
                  <p
                    v-if="
                      previewPost == post.id && previewPostOpened !== post.id
                    "
                    class="article-list-content"
                    :id="post.id"
                  >
                    <b-spinner variant="secondary" type="grow"></b-spinner>
                  </p>
                  <p
                    v-else-if="
                      previewPostOpened == post.id && previewPost == post.id
                    "
                    :id="post.id"
                    :class="'article-list-content ' + previewClass"
                    v-html="previewContent"
                  ></p>
                  <p
                    v-else
                    class="article-list-content"
                    :id="post.id"
                    v-html="post.post_excerpt.nine.substr(0, 80) + '...'"
                  ></p>
                </template>
                <template v-else>
                  <p
                    class="article-list-content"
                    :id="post.id"
                    v-html="post.post_excerpt.nine.substr(0, 80) + '...'"
                  ></p>
                </template>
                <!-- 文章速览 -->

                <div class="article-list-footer">
                  <span class="article-list-date">{{ post.post_date }}</span>
                  <span class="article-list-divider">-</span>
                  <span
                    v-if="post.post_metas.views !== ''"
                    class="article-list-minutes"
                    >{{ post.post_metas.views }}&nbsp;Views</span
                  >
                  <span v-else class="article-list-minutes">0&nbsp;Views</span>
                </div>
              </template>
              <!-- 不包含特色图像文章 -->

              <!-- 包含特色图像文章 -->
              <template
                v-else-if="
                  post.post_img.url &&
                    post.post_categories[0].term_id !== 58 &&
                    !post.post_metas.status
                "
              >
                <div class="article-list-img-else">
                  <div
                    v-if="post.post_categories[0].term_id !== 4"
                    class="article-list-img"
                    :style="'background-image:url(' + post.post_img.url + ')'"
                  ></div>
                  <div
                    :class="
                      post.post_categories[0].term_id == 4
                        ? ''
                        : 'article-list-img-right'
                    "
                  >
                    <template v-if="post.post_categories[0].term_id == 4">
                      <div>
                        <div class="buy-list-item">
                          <div
                            :class="
                              post.post_metas.fineTool.itemImgBorder == 'border'
                                ? 'buy-left-img'
                                : 'buy-left-img-noborder'">
                            <img
                              :src="post.post_img.url"
                              loading="lazy"
                              :alt="post.post_metas.fineTool.itemName"
                            />
                          </div>
                          <div class="buy-right-info">
                            <div>
                              <a
                                :href="post.post_metas.fineTool.itemLink"
                                target="_blank"
                              >
                                <h3
                                  v-html="post.post_metas.fineTool.itemName"
                                ></h3>
                              </a>
                              <p v-html="post.post_metas.fineTool.itemDes"></p>
                            </div>
                            <div>
                              <a
                                :href="post.post_metas.fineTool.itemLink"
                                target="_blank"
                              >
                                {{ post.post_metas.fineTool.itemLinkName }}
                                <span>
                                  <i class="ri-arrow-right-up-line"></i>
                                </span>
                              </a>
                            </div>
                          </div>
                        </div>
                      </div>
                    </template>

                    <div>
                      <template v-if="post.post_categories[0].term_id !== 4">
                        <div>
                          <!-- 置顶文章提示 -->
                          <em
                            class="article-list-type1 sticky-one-tag"
                            v-if="post.sticky"
                          >
                            <i
                              class="czs-arrow-up-l"
                              style="font-size: 14px;font-weight: 600;"
                            ></i>
                            {{ $t("lang.index.atTop") }}
                          </em>
                          <!-- 置顶文章提示 -->
                          <em
                            v-if="post.post_categories[0].term_id === 7"
                            class="article-list-type1"
                          >
                            <b>{{ post.post_categories[0].name }}</b>
                            {{
                              " | " +
                                (post.post_metas.tag_name
                                  ? post.post_metas.tag_name.toUpperCase()
                                  : $t("lang.index.noneTag"))
                            }}
                          </em>
                          <nuxt-link
                            v-else
                            :to="'/cate/' + post.post_categories[0].term_id"
                            class="img-cate list-normal-tag"
                            style="color: rgba(255, 152, 0, 0.83) !important;"
                          >
                            <b>{{ post.post_categories[0].name }}</b>
                            {{
                              " | " +
                                (post.post_metas.tag_name
                                  ? post.post_metas.tag_name.toUpperCase()
                                  : $t("lang.index.noneTag"))
                            }}
                          </nuxt-link>
                        </div>
                      </template>
                      <nuxt-link
                        :to="'/post/' + post.id"
                        style="text-decoration: none;"
                      >
                        <h5
                          v-html="post.title.rendered"
                          style="margin: 0px;padding: 0px;margin-top:15px"
                        ></h5>
                      </nuxt-link>
                      <p
                        v-html="post.post_excerpt.four.substr(0, 65) + '...'"
                        :id="post.id"
                      ></p>
                      <div class="article-list-footer">
                        <nuxt-link
                          v-if="post.post_categories[0].term_id == 4"
                          :to="'/cate/' + post.post_categories[0].term_id"
                          class="article-list-date"
                          v-html="post.post_categories[0].name"
                          style="margin:0px"
                          >{{ post.post_categories[0].name }}</nuxt-link
                        >
                        <span
                          class="article-list-divider"
                          v-if="post.post_categories[0].term_id == 4"
                          >-</span
                        >
                        <span class="article-list-date">{{
                          post.post_date
                        }}</span>
                        <span class="article-list-divider">-</span>
                        <span
                          v-if="post.post_metas.views !== ''"
                          class="article-list-minutes"
                          >{{ post.post_metas.views }}&nbsp;Views</span
                        >
                        <span v-else class="article-list-minutes"
                          >0&nbsp;Views</span
                        >
                      </div>
                    </div>
                  </div>
                </div>
              </template>
              <!-- 包含特色图像文章 -->

              <!-- 状态类型文章 -->
              <template
                v-else-if="
                  post.post_categories[0].term_id === 58 ||
                    !!post.post_metas.status
                "
              >
                <h3
                  class="article-list-content article-status"
                  v-html="post.title.rendered"
                ></h3>
                <div class="article-list-footer">
                  <span
                    :class="
                      'article-list-date ' +
                        (post.post_metas.markCount ? 'mark-div' : '')
                    "
                    @click="addMark(index, post.id)"
                    ><i
                      :class="
                        marking ? 'ri-heart-pulse-fill' : 'ri-heart-3-fill'
                      "
                    ></i>
                    {{
                      post.post_metas.markCount
                        ? post.post_metas.markCount
                        : "0"
                    }}</span
                  >
                  <span class="article-list-divider">-</span>
                  <span class="article-list-date">{{ post.post_date }}</span>
                  <span class="article-list-divider">-</span>
                  <span
                    class="article-list-minutes"
                    v-html="
                      post.post_metas.status
                        ? post.post_metas.status
                        : $t('lang.index.noneMood')
                    "
                  ></span>
                  <template v-if="post.id == 691">
                    <span class="article-list-divider">-</span>
                    <a
                      class="article-list-date gray-for-heroes"
                      @click="grayMode()"
                      >{{ $t("lang.index.moodGray") }}</a
                    >
                  </template>
                </div>
              </template>
              <!-- 状态类型文章 -->
            </li>

            <!-- 无限滚动占位内容 -->
            <mugen-scroll :handler="new_page" :should-handle="loading_first">
              <li class="article-list-item reveal index-post-list bottom" v-if="!loading_end">
                <ContentLoader height="94" width="600" speed="1">
                  <rect x="0" y="0" rx="3" ry="3" width="180" height="94" />
                  <rect x="205" y="0" rx="3" ry="3" width="600" height="40" />
                  <rect x="205" y="48" rx="3" ry="3" width="550" height="20" />
                  <rect x="205" y="80" rx="3" ry="3" width="500" height="15" />
                </ContentLoader>
              </li>
            </mugen-scroll>
            <!-- 无限滚动占位内容 -->
          </ul>
        </div>
      </div>
    </div>
  </client-only>
</template>

<script lang="ts">
import { Component, Vue } from "nuxt-property-decorator";

// import header-top
import headerTop from "../components/top.vue";

// import infinite loading feature
import MugenScroll from "vue-mugen-scroll";
// Vue Content Loader
import { ContentLoader } from "vue-content-loader";

// import jQuery feature
import $ from "jquery";

// imort hightlight.js feature and stylesheet
import hljs from "highlight.js";
import "highlight.js/styles/rainbow.css";

// highlightjs 初始化函数
const highlightCode = (): void => {
  const preEl: any = document.querySelectorAll("pre");
  preEl.forEach((el: any) => {
    hljs.highlightBlock(el);
  });
};

@Component({
  components: {
    headerTop,
    MugenScroll,
    ContentLoader
  }
})
export default class Index extends Vue {
  // Data 数据
  posts: any[] = [];
  posts_id_sticky: string = "0";
  cates: any[] = [];
  tages: any[] = [];
  loading: boolean = true;
  loading_cates: boolean = true;
  loading_tages: boolean = true;
  errored: boolean = true;
  loading_css: string = "";
  version: string = "&categories_exclude=5,2,74";
  previewPost: number = 0;
  previewPostOpened: number = 0;
  previewContent: string = "";
  previewClass: string = "";
  previewClose: number = 0;
  loading_first: boolean = false;
  loading_end: boolean = false;
  notice = {
    visible: false
  } as {
    visible: boolean;
  };
  lang: string = "zh-CN";
  listLoading: any = {};
  paged: number = 1;
  pagedLoading: boolean = false;
  marking: boolean = false;

  //头部信息
  head() {
    return {
      title: "TonyHe - Just A Poor Lifesinger",
      meta: [
        {
          hid: "description",
          name: "description",
          content: "Just A Poor Lifesinger"
        },
        {
          hid: "keywords",
          name: "keywords",
          content:
            "贺莉朋,Tony,TonyHe,博客,个人博客,Personal Blog,blog,wordpress,wordpress theme,wordpress theme tony,tony,tonyhe,snapaper,eugrade,snapaper.com,eugrade.com,zeo.im"
        }
      ]
    };
  }

  // 挂载函数
  mounted() {
    highlightCode();
    // 获取标签
    this.$axios
      .get(
        "https://blog.ouorz.com/wp-json/wp/v2/tags?orderby=count&order=desc&per_page=15"
      )
      .then((response: { data: any }) => {
        this.tages = response.data;
      })
      .finally((): void => {
        this.loading_cates = false;
        let _this: any = this;
        setTimeout(() => {
          _this.loading_tages = false;
        }, 500);
      })
      .catch((): void => {
        this.errored = false;
      });

    //判断 cookie 说明阅读
    if (parseInt((this as any).cookie.get("ouorz_read_cookie")) !== 1) {
      this.notice.visible = true;
    }

    //获取文章列表
    this.$axios
      .get(
        "https://blog.ouorz.com/wp-json/wp/v2/posts?sticky=1&per_page=10" +
          this.version
      )
      .then((res_sticky: { data: any }) => {
        this.posts = res_sticky.data;

        //获取顶置文章 IDs 以在获取其余文章时排除
        let postsLength: number = this.posts.length;
        for (var s = 0; s < postsLength; ++s) {
          this.posts_id_sticky += "," + this.posts[s].id;
        }

        this.$axios
          .get(
            "https://blog.ouorz.com/wp-json/wp/v2/posts?sticky=0&per_page=10&exclude=" +
              this.posts_id_sticky +
              this.version
          )
          .then((res_normal: { data: any }) => {
            //拼接其余文章
            this.posts = this.posts.concat(res_normal.data);
          });
      })
      .catch((): void => {
        this.errored = false;
      })
      .finally((): void => {
        this.loading = false;
        this.loading_first = true;
        this.paged = 2; //加载完1页后累加页数
      });
  }

  //加载下一页文章列表
  new_page(): void {
    //语言包匹配
    if (this.$i18n.locale == "zh-CN") {
      this.listLoading = {
        loading: "加载中",
        list: "文章列表",
        all: "全部文章"
      };
    } else {
      this.listLoading = {
        loading: "Loading",
        list: "Posts List",
        all: "All Posts"
      };
    }
    if (!this.pagedLoading) {
      this.pagedLoading = true;
      $("#view-text").html("-&nbsp;" + this.listLoading.loading + "&nbsp;-");
      this.$axios
        .get(
          "https://blog.ouorz.com/wp-json/wp/v2/posts?sticky=0&exclude=" +
            this.posts_id_sticky +
            "&per_page=10&page=" +
            this.paged +
            this.version
        )
        .then((response: { data: any }) => {
          if (response.data.length !== 0) {
            //判断是否最后一页
            $("#view-text").html("-&nbsp;" + this.listLoading.list + "&nbsp;-");
            this.posts.push.apply(this.posts, response.data); //拼接在上一页之后
            this.paged += 1;
          } else {
            $("#view-text").html("-&nbsp;" + this.listLoading.list + "&nbsp;-");
            this.loading_first = false;
            this.loading_end = true;
          }
          this.pagedLoading = false;
        })
        .catch(() => {
          $("#view-text").html("-&nbsp;" + this.listLoading.all + "&nbsp;-");
          this.paged = 1;
          this.loading_first = false;
          this.loading_end = true;
        });
    }
  }
  // 文章内容快速预览
  preview(postId: number): void {
    $("#btn" + this.previewPost).html("全文速览"); //以防未收起上个预览，更改上个预览按钮
    this.previewPost = postId; //准备预览文章 ID
    this.$axios
      .get("https://blog.ouorz.com/wp-json/wp/v2/posts/" + postId)
      .then(response => {
        this.previewPostOpened = postId; //正在预览文章 ID
        if (response.data.length !== 0) {
          //判断是否最后一页
          $("#btn" + postId).html("收起预览"); //开始预览，更改按钮
          this.previewContent = response.data.content.rendered; //加载文章内容
          this.previewClass = "preview-p"; //更改预览内容块 class
          window.location.hash = "#div" + postId;
        } else {
          this.previewContent = "这里什么都没有..."; //无内容默认展示
          this.previewClass = "preview-p"; //更改预览内容区块 class
        }
      })
      .catch(error => {
        console.log(error);
      });
  }
  // 关闭文章内容快速预览
  closePreview(postId: number): void {
    $("#btn" + postId).html("全文速览"); //收起预览，更改按钮
    this.previewClose = postId; //收起预览的文章 ID
    this.previewContent = ""; //初始化预览内容
    this.previewPost = 0; //初始化准备预览文章 ID
    this.previewPostOpened = 0; //初始化正在预览文章 ID
    this.previewClass = ""; //初始化预览内容块 class
    this.previewClose = 0; //初始化已收起预览的文章 ID
  }
  // 关闭 cookies 使用提示
  discard_notice(): void {
    (this as any).cookie.set("ouorz_read_cookie", 1);
    this.notice.visible = false;
  }
  // 黑白模式
  grayMode(): void {
    if ($("html").attr("class") !== "gray-mode") {
      $("html").attr("class", "gray-mode");
    } else {
      $("html").attr("class", "");
    }
  }
  // Mark 操作
  addMark(index: number, id: number): void {
    this.marking = true;
    this.$axios
      .get("https://blog.ouorz.com/wp-json/tony/v1/mark/" + id)
      .then(response => {
        this.posts[
          index
        ].post_metas.markCount = response.data.markCountNow.toString();
        this.marking = false;
      })
      .catch(error => {
        console.log(error);
      });
  }
  // 监听页面变化
  updated() {
    // 页面内容变化时执行代码渲染
    highlightCode();
  }
}
</script>
