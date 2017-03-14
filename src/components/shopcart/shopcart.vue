<template>
  <div class="shopcart">
    <div class="content">
      <div class="content-left">
        <div class="logo-wrap">
          <div class="logo">
            <span class="icon-shopping_cart"></span>
          </div>
        </div>
        <div class="price"></div>
        <div class="desc"></div>
      </div>
      <div class="content-right">{{ }}</div>
    </div>
  </div>
</template>

<script type="text/ecmascript-6">
  export default {};
</script>

<style lang="stylus" rel="stylesheet/stylus">
  .shopcart
    position: fixed
    bottom: 0
    left: 0
    width: 100%
    height: 48px
    background-color: #141d27
    .content
      display: flex
      height: 100%
      .content-left
        flex: 1
      .content-right
        width: 105px
        height: 100%
        background-color: rgba(255, 255, 255, .2)
</style>