<template>
  <div class="goods">
    <div class="nav">

      <div class="w">
        <a href="javascript:;" :class="{active:sortType===1}" @click="reset()">综合排序</a>
        <a href="javascript:;" @click="sort(1)" :class="{active:sortType===2}">价格从低到高</a>
        <a href="javascript:;" @click="sort(-1)" :class="{active:sortType===3}">价格从高到低</a>
        <div class="price-interval">
          <input type="number" class="input" placeholder="价格" v-model="params.min">
          <span style="margin: 0 5px"> - </span>
          <input type="number" placeholder="价格" v-model="params.max">
          <y-button text="确定" classStyle="main-btn" @btnClick="reset" style="margin-left: 10px;"></y-button>
        </div>
      </div>
    </div>

    <!--商品-->
    <div class="goods-box w">
      <mall-goods v-for="(item,i) in computer" :key="i" :msg="item"></mall-goods>
    </div>
    <div v-show="!busy"
         class="w load-more"
         v-infinite-scroll="loadMore"
         infinite-scroll-disabled="busy"
         infinite-scroll-distance="100">
      正在加载中...
    </div>
  </div>
</template>
<script>
  import {getComputer} from '/api/goods'
  import mallGoods from '/components/mallGoods'
  import YButton from '/components/YButton'
  export default {
    data () {
      return {
        computer: [],
        ids: ["http://image.smartisanos.cn/resource/f99eb08b801736e22479e50470567146.png", "http://image.smartisanos.cn/resource/bf99a58daeb23de22cbca066de5e2af9.png", "http://image.smartisanos.cn/resource/6130f99fbf9fc67f2f39eaf9652a1e9a.png", "http://image.smartisanos.cn/resource/0b55e64f8a8982febe19ca9d1b790b9c.png", "http://image.smartisanos.cn/resource/fee4687204600b791985174e7df0b124.png", "http://image.smartisanos.cn/resource/c2b673ba1f71dc2e7d104b5d7964a066.png", "http://image.smartisanos.cn/resource/3c0c6c34b7efd2b559e021b4f7373273.png", "http://image.smartisanos.cn/resource/1fbf7021c394bc9c0db57da9471b7fc8.png"],
        busy: false,
        timer: null,
        sortType: 1,
        windowHeight: null,
        windowWidth: null,
        params: {
          page: 1,  // 页码
          sort: '', // 排序
          min: '',  // 最小价格
          max: ''
        }
      }
    },
    methods: {
      _getComputer (flag) {
        const {page, sort, min, max} = this.params
        let params = {
          page,
          sort,
          priceGt: min,
          priceLte: max
        }
        getComputer(params).then(res => {
          if (res.result.count) {
            let data = res.result.data
            let idList = this.ids
            //  data = data.filter(item=>{return this.idContain(idList,item);})
            if (flag) {
              this.computer = this.computer.concat(data)
            } else {
              this.computer = data
            }
          } else {
            clearTimeout(this.timer)
            this.busy = true
          }
        })
      },
      idContain(idList,item){
        for (const key in idList) {
         
            const element = idList[key];
            let path=item.productImageBig;
            console.log(path.substring(path.indexOf('resource/')+'resource/'.length,path.indexOf('.jpg')))
            if(element==path){
              
              return true;
            }
          
        }
        return false;
      },
      
      // 默认排序
      reset () {
        this.sortType = 1
        this.params.sort = ''
        this.params.page = 1
        this.busy = false
        this._getComputer()
      },
      // 价格排序
      sort (v) {
        v === 1 ? this.sortType = 2 : this.sortType = 3
        this.params.sort = v
        this.params.page = 1
        this.busy = false
        this._getComputer()
      },
      // 加载更多
      loadMore () {
        this.busy = true
        this.timer = setTimeout(() => {
          this.params.page++
          this._getComputer(true)
          this.busy = false
        }, 500)
      }
    },
    created () {
      this._getComputer()
    },
    mounted () {
      this.windowHeight = window.innerHeight
      this.windowWidth = window.innerWidth
    },
    components: {
      mallGoods,
      YButton
    }
  }
</script>
<style lang="scss" rel="stylesheet/scss" scoped>
  @import "../../assets/style/mixin";
  @import "../../assets/style/theme";

  .nav {
    height: 60px;
    line-height: 60px;
    > div {
      display: flex;
      align-items: center;
      a {
        padding: 0 15px;
        height: 100%;
        @extend %block-center;
        font-size: 12px;
        color: #999;
        &.active {
          color: #5683EA;
        }
        &:hover {
          color: #5683EA;
        }
      }
      input {
        @include wh(80px, 30px);
        border: 1px solid #ccc;
      }
      input + input {
        margin-left: 10px;
      }
    }
    .price-interval {
      padding: 0 15px;
      @extend %block-center;
      input[type=number] {
        border: 1px solid #ccc;
        text-align: center;
        background: none;
        border-radius: 5px;
      }
    }
  }
  .load-more {
    text-align: center;background: #fff
  }
  .goods-box {
    > div {
      float: left;
      border: 1px solid #efefef;
    }
  }


</style>
