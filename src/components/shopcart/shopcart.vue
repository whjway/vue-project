<template>
  <div class="shopcart">
    <div class="content">
      <div class="content-left">
        <div class="logo-wrap">
          <div class="logo" :class="{'highlight': totalCount>0}">
            <span class="icon-shopping_cart" :class="{'highlight': totalCount>0}"></span>
          </div>
          <div class="num" v-show="totalCount>0">{{totalCount}}</div>
        </div>
        <div class="price" :class="{'highlight': totalCount>0}">{{totalPrice | currency '￥' 0}}元</div>
        <div class="desc">另需配送费{{deliveryPrice | currency '￥' 0}}元</div>
      </div>
      <div class="content-right">
        <div class="pay" :class="payClass">{{payDesc}}</div>
      </div>
    </div>
    <div class="ball-container">
      <div class="ball" transition="drop" v-for="ball in balls" v-show="ball.show">
        <div class="inner inner-hook"></div>
      </div>
    </div>
  </div>
</template>

<script type="text/ecmascript-6">
  export default {
    data() {
      return {
        balls: [
          {
            show: false
          },
          {
            show: false
          },
          {
            show: false
          },
          {
            show: false
          },
          {
            show: false
          }
        ],
        dropBalls: []
      };
    },
    props: {
      selectFoods: {
        type: Array,
        default() {
          return [
            {
//              price: 10,
//              count: 2
            }
          ];
        }
      },
      deliveryPrice: {
        type: Number,
        default: 0
      },
      minPrice: {
        type: Number,
        default: 0
      }
    },
    computed: {
      totalPrice() {
        let total = 0;
        this.selectFoods.forEach((food) => {
          total += food.price * food.count;
        });
        return total;
      },
      totalCount() {
        let count = 0;
        this.selectFoods.forEach((food) => {
          count += food.count;
        });
        return count;
      },
      payDesc() {
        if (this.totalPrice === 0) {
          return `￥${this.minPrice}元起送`;
        } else if (this.totalPrice >= this.minPrice) {
          return '去结算';
        } else if (this.totalPrice < this.minPrice) {
          let diff;
          diff = this.minPrice - this.totalPrice;
          return `还差￥${diff}元起送`;
        }
      },
      payClass() {
        if (this.totalPrice < this.minPrice) {
          return 'not-enough';
        } else {
          return 'enough';
        }
      }
    },
    methods: {
      drop(el) {
        for (let i = 0; i < this.balls.length; i++) {
          let ball = this.balls[i];
          if (!ball.show) {
            ball.show = true;
            ball.el = el;
            this.dropBalls.push(ball);
            return;
          }
        }
      }
    },
    transitions: {
      drop: {
        beforeEnter(el) {
          let count = this.balls.length;
          while (count--) {
            let ball = this.balls[count];
            if (ball.show) {
              let rect = ball.el.getBoundingClientRect();
              let x = rect.left - 32;
              let y = -(window.innerHeight - rect.top - 22);
              el.style.display = '';
              el.style.webkitTransform = `translate3d(0,${y}px,0)`;
              el.style.transform = `translate3d(0,${y}px,0)`;
              let inner = el.querySelector('.inner-hook');
              inner.style.transform = `translate3d(${x}px,0,0)`;
              inner.style.webkitTransform = `translate3d(${x}px,0,0)`;
            }
          }
        },
        enter(el) {
          /* eslint-disable no-unused-vars */
          // 触发重绘
          let rf = el.offsetHeight;
          this.$nextTick(() => {
            el.style.webkitTransform = 'translate3d(0,0,0)';
            el.style.transform = 'translate3d(0,0,0)';
            let inner = el.querySelector('.inner-hook');
            inner.style.transform = 'translate3d(0,0,0)';
            inner.style.webkitTransform = 'translate3d(0,0,0)';
          });
        },
        afterEnter(el) {
          let ball = this.dropBalls.shift();
          if (ball) {
            ball.show = false;
            el.style.display = 'none';
          }
        }
      }
    }
  };
</script>

<style lang="stylus" rel="stylesheet/stylus">
  .shopcart
    position: fixed
    bottom: 0
    left: 0
    z-index: 50
    width: 100%
    height: 48px
    background-color: #141d27
    font-size: 0
    .content
      display: flex
      height: 100%
      color: rgba(255, 255, 255, .4)
      .content-left
        flex: 1
        .logo-wrap
          display: inline-block
          vertical-align: top
          position: relative
          top: -10px
          margin: 0 12px
          padding: 6px
          width: 56px
          height: 56px
          box-sizing: border-box
          border-radius: 50%
          background-color: #141d27
          .logo
            width: 100%
            height: 100%
            border-radius: 50%
            background-color: #2b343c
            text-align: center
            &.highlight
              background-color: rgb(0, 160, 220)
            .icon-shopping_cart
              line-height: 44px
              font-size: 24px
              color: #80858a
              &.highlight
                color: #fff
          .num
            position: absolute
            top: 0
            left: 34px
            /*width: 24px*/
            height: 16px
            padding: 0 6px
            line-height: 16px
            text-align center
            border-radius: 16px
            font-size: 9px
            font-weight: 700
            color: #fff
            background-color: rgb(240, 20, 20)
            box-shadow: 0 4px 8px rgba(0, 0, 0, .4)
        .price
          display: inline-block
          vertical-align: top
          margin-top: 12px
          line-height: 24px
          padding-right: 12px
          box-sizing: border-box
          border-right: 1px solid rgba(255, 255, 255, .1)
          font-size: 16px
          font-weight: 700
          &.highlight
            color: #fff
        .desc
          display: inline-block
          vertical-align: top
          margin-top: 12px
          line-height: 24px
          padding-left: 12px
          box-sizing: border-box
          font-size: 10px
          font-weight: 400
      .content-right
        width: 105px
        height: 100%
        .pay
          height: 100%
          background-color: rgba(255, 255, 255, .2)
          line-height: 48px
          font-size: 12px
          text-align: center
          font-weight: 700
          &.not-enough
            background-color: rgba(255, 255, 255, .2)
          &.enough
            background-color: #00b43c
            color: #fff
    .ball-container
      .ball
        position: fixed
        left: 32px
        bottom: 22px
        z-index: 200
        &.drop-transition
          transition: all .4s cubic-bezier(.49, -.29, .75, .41)
          .inner
            width: 16px
            height: 16px
            border-radius: 50%
            background-color: rgb(0, 160, 220)
            transition: all .4s linear

</style>