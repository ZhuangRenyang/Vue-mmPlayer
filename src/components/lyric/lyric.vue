<template>
  <div>
    <!--封面-->
    <dl class="music-info">
      <dt>
        <img :src="musicPicUrl" />
      </dt>
      <template v-if="currentMusic.id">
        <dd>歌曲名：{{ currentMusic.name }}</dd>
        <dd>歌手名：{{ currentMusic.singer }}</dd>
        <dd>专辑名：{{ currentMusic.album }}</dd>
      </template>
      <template v-else>
        <dd>虹色轨迹🌠在线音乐播放器</dd>
        <dd>
          <a class="hover" target="_blank" href="https://github.com/maomao1996">
            <mm-icon type="github" :size="14" />
            &nbsp;虹色轨迹🌠
          </a>
        </dd>
      </template>
    </dl>
    <!--歌词-->
    <div ref="musicLyric" class="music-lyric">
      <div class="music-lyric-items" :style="lyricTop">
        <p v-if="!currentMusic.id">还没有播放音乐哦！</p>
        <p v-else-if="nolyric">暂无歌词！</p>
        <template v-else-if="lyric.length > 0">
          <p v-for="(item, index) in lyric" :key="index" :class="{ on: lyricIndex === index }">
            {{ item.text }}
          </p>
        </template>
        <p v-else>歌词加载失败！</p>
      </div>
    </div>
  </div>
</template>

<script>
import { mapGetters } from 'vuex'

export default {
  name: 'Lyric',
  props: {
    // 歌词数据
    lyric: {
      type: Array,
      default: () => [],
    },
    // 是否无歌词
    nolyric: {
      type: Boolean,
      default: false,
    },
    // 当前歌词下标
    lyricIndex: {
      type: Number,
      default: 0,
    },
  },
  data() {
    return {
      top: 0, // 歌词居中
    }
  },
  computed: {
    musicPicUrl() {
      return this.currentMusic.id
        ? `${this.currentMusic.image}?param=300y300`
        : require('../../assets/img/player_cover.png')
    },
    lyricTop() {
      return `transform :translate3d(0, ${-34 * (this.lyricIndex - this.top)}px, 0)`
    },
    ...mapGetters(['currentMusic']),
  },
  mounted() {
    window.addEventListener('resize', () => {
      clearTimeout(this.resizeTimer)
      this.resizeTimer = setTimeout(() => this.clacTop(), 60)
    })
    this.$nextTick(() => this.clacTop())
  },
  methods: {
    // 计算歌词居中的 top值
    clacTop() {
      const dom = this.$refs.musicLyric
      const { display = '' } = window.getComputedStyle(dom)
      if (display === 'none') {
        return
      }
      const height = dom.offsetHeight
      this.top = Math.floor(height / 34 / 2)
    },
  },
}
</script>

<style lang="less" scoped>
.music-info {
  padding-bottom: 20px;
  text-align: center;
  font-size: @font_size_medium;
  dt {
    position: relative;
    width: 186px;
    height: 186px;
    margin: 0 auto 15px;
    &:after {
      content: '';
      position: absolute;
      left: 9px;
      top: 0;
      width: 201px;
      height: 180px;
      background: url('~assets/img/album_cover_player.png') 0 0 no-repeat;
    }
    img {
      vertical-align: middle;
      width: 186px;
      height: 186px;
    }
  }
  dd {
    height: 30px;
    line-height: 30px;
    .no-wrap();
  }
}

/*歌词部分*/
.music-lyric {
  position: absolute;
  top: 315px;
  right: 0;
  bottom: 0;
  left: 0;
  overflow: hidden;
  text-align: center;
  mask-image: linear-gradient(
    to bottom,
    rgba(255, 255, 255, 0) 0,
    rgba(255, 255, 255, 0.6) 15%,
    rgba(255, 255, 255, 1) 25%,
    rgba(255, 255, 255, 1) 75%,
    rgba(255, 255, 255, 0.6) 85%,
    rgba(255, 255, 255, 0) 100%
  );
  .music-lyric-items {
    text-align: center;
    line-height: 34px;
    font-size: @font_size_small;
    transform: translate3d(0, 0, 0);
    transition: transform 0.6s ease-out;
    .no-wrap();
    .on {
      color: @lyric_color_active;
    }
  }
}

// 当屏幕小于 960 时
@media (max-width: 960px) {
  .music-info {
    display: none;
  }
  .music-lyric {
    top: 0;
  }
}
</style>
