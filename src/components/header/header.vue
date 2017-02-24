<template>
  <div class='header'>
    <div class="content-wrap">
      <div class="avatar">
        <img :src="seller.avatar" width="64" height="64"/>
      </div>
      <div class="content">
        <div class="title">
          <span class="brand"></span>
          <span class="name">{{seller.name}}</span>
        </div>
        <div class="description">
          {{seller.description}}/{{seller.deliveryTime}}分钟送达
        </div>
        <div v-if="seller.supports" class="support">
          <span :class="classMap[seller.supports[0].type]" class="icon"></span>
          <span class="text">
            {{seller.supports[0].description}}
          </span>
        </div>
      </div>
      <div @click="showDetail" v-if="seller.supports" class="support-count">
        <span class="count">{{seller.supports.length}}个</span>
        <i class="icon-keyboard_arrow_right"></i>
      </div>
    </div>
    <div @click="showDetail" class="bulletin-wrap">
      <span class="bulletin-title">公告</span>
      <span class="bulletin-text">{{seller.bulletin}}</span>
      <span class="icon-keyboard_arrow_right"></span>
    </div>
    <div class="bg">
      <img :src="seller.avatar" width="100%" height="100%"/>
    </div>
    <div v-show="detailShow" class="detail" transition="fade">
      <!--CSS Sticky Footer-->
      <div class="detail-wrap clearfix">
        <div class="detail-main">
          <h1 class="name">{{seller.name}}</h1>
          <div class="star-warp">
            <star :size="48" :score="seller.score"></star>
          </div>
          <div class="title">
            <div class="line"></div>
            <div class="text">优惠信息</div>
            <div class="line"></div>
          </div>
          <ul v-if="seller.supports"  class="support-wrap">
            <li class="support" v-for="item in seller.supports">
              <span :class="classMap[item.type]" class="icon"></span>
              <span class="text">
            {{item.description}}
          </span>
            </li>
          </ul>
          <div class="title">
            <div class="line"></div>
            <div class="text">商家公告</div>
            <div class="line"></div>
          </div>
          <div class="bulletin-detail">{{seller.bulletin}}</div>
        </div>
      </div>
      <div class="detail-close" @click="closeDetail" >
        <i class="icon-close"></i>
      </div>
    </div>
  </div>
</template>

<script type="text/ecmascript-6">
  import star from 'components/star/star.vue';
  export default {
    props: {
      seller: {
        type: Object
      }
    },
    components: {
      'star': star
    },
    data() {
      return {
        detailShow: false
      };
    },
    methods: {
      closeDetail() {
        this.detailShow = false;
      },
      showDetail() {
        this.detailShow = true;
      }
    },
    created() {
      this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee'];
    }
  };
</script>

<style lang="stylus" rel="stylesheet/stylus">

  @import "../../common/stylus/mixin"

  .header
    position: relative
    overflow: hidden
    color: rgb(255, 255, 255)
    background-color: rgba(7, 17, 27, .5)
    .content-wrap
      position: relative
      padding: 24px 12px 18px 24px
      font-size 0
      .avatar, .content
        display: inline-block
      .avatar
        vertical-align: top
        img
          border-radius: 2px
      .content
        padding: 2px 0 2px 16px
        .title
          font-size: 16px
          font-weight: bold
          line-height: 18px
          .brand
            display: inline-block
            vertical-align: top
            width: 30px
            height: 18px
            bg-image('brand')
            background-size: 100%
        .description
          font-size: 12px
          font-weight: 200
          line-height: 12px
          margin: 8px 0 10px
        .support
          font-size: 10px
          font-weight: 200
          line-height: 12px
          .icon
            display: inline-block
            vertical-align: top
            width: 12px
            height: 12px
            margin-right: 4px
            background-size: 12px 12px
            background-repeat: no-repeat
            &.decrease
              bg-image('decrease_1')
            &.discount
              bg-image('discount_1')
            &.guarantee
              bg-image('guarantee_1')
            &.invoice
              bg-image('invoice_1')
            &.special
              bg-image('special_1')
      .support-count
        position: absolute
        right: 12px
        bottom: 14px
        padding: 0 8px
        height: 24px
        line-height: 24px
        background-color: rgba(0, 0, 0, .2)
        border-radius: 14px
        text-align: center
        .count
          vertical-align: top
          font-size: 10px
        i
          /*height: 12px*/
          line-height: 24px
          margin-left: 2px
          font-size: 10px

    .bulletin-wrap
      position: relative
      padding: 0 12px
      font-size: 0
      background-color: rgba(7, 17, 27, .2)
      .bulletin-title
        vertical-align: top
        padding: 0 2px
        line-height: 28px
        font-size: 10px
        border-radius 2px
        color: #000
        background-color: #fff
      .bulletin-text
        vertical-align: top
        display: inline-block
        width: calc(100% - 40px)
        height: 28px
        line-height: 28px
        padding: 0 4px
        font-size: 10px
        overflow: hidden;
        text-overflow: ellipsis;
        white-space: nowrap;
      .icon-keyboard_arrow_right
        position: absolute
        right: 12px
        line-height: 28px
        font-size: 10px
    .bg
      position: absolute
      top: 0
      z-index: -1
      width: 100%
      height: 100%
      filter: blur(10px)
    .detail
      position: fixed
      z-index: 100
      top: 0
      left: 0
      width: 100%
      height: 100%
      overflow: auto
      backdrop-filter: blur(10px)
      transition: all .5s
      &.fade-transition
        opacity: 1
        background-color: rgba(7, 17, 27, .8)
      &.fade-enter,&.fade-leave
        opacity: 0
        background-color: rgba(7, 17, 27, 0)
      .detail-wrap
        min-height: 100%
        width: 100%
        .detail-main
          margin-top: 64px
          padding: 0 36px 64px
          .name
            font-size 16px
            line-height: 16px
            font-weight: 700
            text-align: center
          .star-warp
            margin-top: 16px
            padding: 2px 0
            text-align: center
          .title
            display: flex
            margin: 28px auto 24px
            .line
              flex: 1
              position: relative
              top: 6px
              border-top: 1px solid rgba(255, 255, 255, .2)
            .text
              margin: 0 12px
              line-height: 14px
              font-size: 14px
              font-weight: 700
              color: gba(255, 255, 255)
          .bulletin-detail
            padding: 0 12px
            line-height: 28px
            font-size 12px
          .support-wrap
            padding: 0 12px

            .support
              font-size: 12px
              font-weight: 200
              line-height: 16px
              margin-bottom: 12px
              &:last-child
                margin-bottom: 0
              .icon
                display: inline-block
                vertical-align: top
                width: 16px
                height: 16px
                margin-right: 6px
                background-size: 16px 16px
                background-repeat: no-repeat
                &.decrease
                  bg-image('decrease_2')
                &.discount
                  bg-image('discount_2')
                &.guarantee
                  bg-image('guarantee_2')
                &.invoice
                  bg-image('invoice_2')
                &.special
                  bg-image('special_2')

      .detail-close
        position: relative
        width: 32px
        height: 32px
        margin: -64px auto 0 auto
        clear: both
        font-size 32px
        color: rgba(255, 255, 255, .5)
</style>
