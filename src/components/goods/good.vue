<template>
    <div class="goods">
        <div class="menu-wrapper" ref="menuwrapper">
            <ul>
                <li v-for="(item,index) in goods" class="menu-item" :class="{'current':currentIndex===index}" @click="selectMenu(index,$event)">
                    <span class="text border-1px">
                      <span v-show="item.type>0" class="icon" :class="ClassMap[item.type]"></span>{{item.name}}
                    </span>
                </li>
            </ul>
        </div>
        <div class="foods-wrapper" ref="foodswrapper">
          <ul>
            <li v-for="item in goods" class="foods-list food-list-hook">
              <h1 class="title">{{item.name}}</h1>
              <ul>
                  <li v-for="food in item.foods" class="food-item border-1px">
                      <div class="icon">
                        <img height="48" :src="food.icon">
                      </div>
                      <div class="content">
                          <h1 class="name">{{food.name}}</h1>
                          <p class="desc">{{food.description}}</p>
                          <div class="extra">
                              <span class="count">月售{{food.sellCount}}份</span><span>好评率{{food.rating}}%</span>
                          </div>
                          <div class="price">
                              <span class="newprice">¥{{food.price}}</span><span class="old" v-show="food.oldPrice">
                                  ¥{{food.oldPrice}}
                              </span>
                          </div>
                      </div>
                  </li>
              </ul>
            </li>
          </ul>
        </div>
        <shopcart :delivery-price="seller.deliveryPrice" :min-price="seller.minPrice"></shopcart>
    </div>
</template>

<script type="text/ecmascript-6">
    import BScroll from 'better-scroll';
    import shopcart from 'components/shopcart/shopcart.vue';
    const ERR_OK = 0;
    export default{
        name: 'goods',
        props: {
          seller: {
            type: Object
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
            }
        },
        methods: {
          _initScroll() {
              this.menuScroll = new BScroll(this.$refs.menuwrapper, {
                    click: true
              });
              this.foodsScroll = new BScroll(this.$refs.foodswrapper, {
                  probeType: 3
              });
              this.foodsScroll.on('scroll', (pos) => {
                  this.scrollY = Math.abs(Math.round(pos.y));
              });
          },
          _calculateHeight() {
              let foodList = this.$refs.foodswrapper.getElementsByClassName('food-list-hook');
              let height = 0;
              this.listHeight.push(height);
              for (let i = 0; i < foodList.length; i++) {
                let item = foodList[i];
                height += item.clientHeight;
                this.listHeight.push(height);
              }
          },
          selectMenu(index, event) {
            if (!event._constructed) {
                return;
            }
            let foodList = this.$refs.foodswrapper.getElementsByClassName('food-list-hook');
            let el = foodList[index];
            this.foodsScroll.scrollToElement(el, 300);
          }
        },
        created() {
            this.ClassMap = ['decrease', 'discount', 'guarantee', 'invoice', 'special'];
            this.$http.get('/api/goods').then((response) => {
                response = response.body;
                if (response.errno === ERR_OK) {
                    this.goods = response.data;
                    this.$nextTick(() => {
                      this._initScroll();
                      this._calculateHeight();
                    });
                }
            });
        },
        components: {shopcart}
    };
</script>
<style lang="stylus" rel="stylesheet/stylus">
    @import "../../common/stylus/minxin.styl";
    .goods
        position:absolute
        display: flex
        width:100%
        top: 174px
        bottom:46px
        overflow :hidden
        .menu-wrapper
          flex:0 0 80px
          width:80px
          background:#f4f5f8
          .menu-item
            display:table
            height:54px
            width:56px
            line-height:14px
            padding:0 12px
            &.current
              position:relative
              z-index:10
              margin-top: -1px
              font-weight:700
              background:lightsalmon
              .text
                border-none()
            .icon
              display: inline-block
              vertical-align: top
              width: 12px
              height: 12px
              margin-right: 2px
              background-size: 12px 12px
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
            .text
              display: table-cell
              width: 56px
              vertical-align: middle
              border-1px(rgba(7, 17, 27, 0.1))
              font-size: 12px
        .foods-wrapper
          flex:1
          .title
            padding-left: 4px
            height:26px
            line-height:26px
            border-left:2px solid #dddde1
            font-size: 12px
            color:rgb(147,153,159)
            background:#f3f5f7
          .food-item
            display:flex
            margin:18px
            padding-bottom:18px
            border-1px(rgb(147,153,143,0.1))
            &last-child
              border-none()
              margin-bottom:0
            .icon
              flex:0 0 57px
              margin-right:10px
            .content
              flex:1
              .name
                margin:2px 0 8px 0
                height:14px
                line-height:14px
                font-size: 14px
                color:rgb(7,17,27)
              .desc
                margin-bottom:8px
                line-height:12px
                font-size: 10px
                color:rgb(147,153,159)
              .extra
                line-height: 10px
                font-size: 10px
                color:rgb(147,153,159)
                .count
                  margin-right: 13px
              .price
                font-weight:700
                line-height:24px
                .newprice
                  margin-right: 8px
                  font-size:14px
                  color:red
                .old
                  text-decoration:line-through
                  font-size:10px
                  color:rgb(147,153,159)

</style>
