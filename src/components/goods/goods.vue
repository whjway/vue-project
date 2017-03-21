<template>
  <div class="goods">
    <!--左侧导航-->
    <div class="menu-wrap" v-el:menu-wrap>
      <ul>
        <li v-for="item in goods" class="menu-item" :class="{'current':currentIndex === $index}"
            @click.stop="selectMenu($index,$event)">
          <div class="text border-1px">
            <span v-show="item.type>0" :class="classMap[item.type]" class="icon"></span><span>{{item.name}}</span>
          </div>
        </li>
      </ul>
    </div>
    <!--右侧内容-->
    <div class="foods-wrap" v-el:foods-wrap>
      <ul>
        <!--内容分类-->
        <li v-for="item in goods" class="foods-list foods-list-hook">
          <h1 class="title">{{item.name}}</h1>
          <!--商品列表-->
          <ul>
            <li v-for="good in item.foods" class="food-item border-1px">
              <div class="icon">
                <img :src="good.icon" width="57" height="57"/>
              </div>
              <div class="content">
                <h2 class="content-title">{{good.name}}</h2>
                <p class="content-desc">{{good.description}}</p>
                <div class="extra">
                  <span class="sellcount">月售{{good.sellCount}}份</span><span>好评率{{good.rating}}%</span>
                </div>
                <div class="price">
                  <span class="nowprice"><span class="oldprice">￥</span>{{good.price}}</span><span
                    v-show="good.oldPrice" class="oldprice">{{good.oldPrice | currency '￥' 0}}</span>
                </div>
                <div class="cartcontrol-wrap">
                  <cartcontrol :food="good"></cartcontrol>
                </div>
              </div>
            </li>
          </ul>
        </li>
      </ul>
    </div>
    <!--购物车-->
    <shopcart v-ref:shopcart :delivery-price="seller.deliveryPrice" :min-price="seller.minPrice"
              :select-foods="selectFoods"></shopcart>
  </div>
</template>

<script type="text/ecmascript-6">
  import BScroll from 'better-scroll';
  import shopcart from 'components/shopcart/shopcart';
  import cartcontrol from 'components/cartcontrol/cartcontrol';
  // 请求成功
  const ERR_OK = 0;

  export default {
    props: {
      seller: {
        type: Object
      }
    },
    components: {
      shopcart,
      cartcontrol
    },
    events: {
      // 接受cartcontrol派发的DOM
      'cart.add'(target) {
        // 调用自己的方法->获取子组件->调用子组件对应方法
        this._drop(target);
      }
    },
    data() {
      return {
        goods: [],
        listHeight: [],
        scrollY: 0
      };
    },
    computed: {
      currentIndex() {
        for (let i = 0; i < this.listHeight.length; i++) {
          let height1 = this.listHeight[i];
          let height2 = this.listHeight[i + 1];
          if (!height2 || (this.scrollY >= height1 && this.scrollY < height2)) {
            return i;
          }
        }
        return 0;
      },
      selectFoods() {
        let foods = [];
        this.goods.forEach((good) => {
          good.foods.forEach((food) => {
            if (food.count) {
              foods.push(food);
            }
          });
        });
        return foods;
      }
    },
    methods: {
      // menu点击定位
      selectMenu(index, event) {
        if (!event._constructed) {
          return;
        }
        let foodsList = this.$els.foodsWrap.getElementsByClassName('foods-list-hook');
        let el = foodsList[index];
        this.foodsScroll.scrollToElement(el, 300);
      },
      // 初始化BScroll
      _initScroll() {
        this.menuScroll = new BScroll(this.$els.menuWrap, {
          click: true
        });
        this.foodsScroll = new BScroll(this.$els.foodsWrap, {
          probeType: 3,
          click: true
        });
        this.foodsScroll.on('scroll', (pos) => {
          this.scrollY = Math.abs(Math.round(pos.y));
        });
      },
      // 计算food高度
      _calculateHeight() {
        let foodsList = this.$els.foodsWrap.getElementsByClassName('foods-list-hook');
        let height = 0;
        this.listHeight.push(height);
        for (let i = 0; i < foodsList.length; i++) {
          let food = foodsList[i];
          height += food.clientHeight;
          this.listHeight.push(height);
        }
      },
      // 调用子组件方法 计算小球初始位置
      _drop(target) {
        // 体验优化，异步执行下落动画
        this.$nextTick(() => {
          // 获取子组件->调用子组件对应方法
          this.$refs.shopcart.drop(target);
        });
      }
    },
    created() {
      this.$http.get('api/goods').then((response) => {
        response = response.body;
        if (response.errno === ERR_OK) {
          this.goods = response.data;
          this.$nextTick(() => {
            this._initScroll();
            this._calculateHeight();
          });
        }
      });

      this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee'];
    },
    ready() {
    }
  };
</script>

<style lang="stylus" rel="stylesheet/stylus">

  @import "../../common/stylus/mixin"

  .goods
    display: flex
    position: absolute
    top: 174px
    bottom: 46px
    width: 100%
    overflow: hidden
    .menu-wrap
      flex: 0 0 80px
      width: 80px
      /*overflow-y: auto*/
      background-color: #f3f5f7
      .menu-item
        display: table
        height: 54px
        width: 56px
        line-height: 14px
        padding: 0 12px
        &.current
          position: relative
          top: -1px
          z-index: 10
          font-weight: 700
          background-color: #fff
          .text
            border-none()
          span
            font-weight: 700
      .text
        display: table-cell
        width: 56px
        line-height: 16px
        vertical-align: middle
        font-size: 12px
        border-1px(rgba(7, 17, 27, .1))
      .icon
        display: inline-block
        vertical-align: top
        width: 16px
        height: 16px
        margin-right: 2px
        background-size: 16px 16px
        background-repeat: no-repeat
        &.decrease
          bg-image('decrease_3')
        &.discount
          bg-image('discount_3')
        &.guarantee
          bg-image('guarantee_3')
        &.invoice
          bg-image('invoice_3')
        &.special
          bg-image('special_3')
    .foods-wrap
      flex: 1
      .title
        height: 26px
        line-height: 26px
        padding-left: 24px
        border-left: 2px solid #d9dde1
        background-color: #f3f5f7
        color: rgb(147, 153, 159)
        font-size: 12px
      .food-item
        display: flex
        padding: 18px 0
        margin: 0 18px
        border-1px(rgba(7, 17, 27, .1))
        &:last-child
          border-none()
        .icon
          flex: 0 0 57px
          margin-right: 10px
        .content
          flex: 1
          .content-title
            margin: 2px 0 8px
            line-height: 14px
            font-size: 14px
            color: rgb(7, 17, 27)
          .content-desc, .extra
            line-height: 10px
            font-size: 10px
            color: rgb(147, 153, 159)
            margin-top: 8px
          .sellcount
            margin-right: 12px
          .price
            line-height: 24px
            font-weight: 700
            .nowprice
              margin-right: 8px
              font-size: 14px
              color: rgb(240, 20, 20)
              .oldprice
                color: rgb(240, 20, 20)
                text-decoration: none
            .oldprice
              text-decoration: line-through
              font-size: 10px
              color: rgb(147, 153, 159)
          .cartcontrol-wrap
            position: absolute
            right: 0
            bottom: 12px
</style>