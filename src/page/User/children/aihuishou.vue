<template>
  <div>
    <y-shelf title="商品发布">
      <span slot="right"><y-button text="添加商品" style="margin: 0" @btnClick="update()"></y-button></span>
      <div slot="content">
        <div v-if="addList.length">
          <div class="address-item" v-for="(item,i) in addList" :key="i">
            <div class="name">{{item.productName}}</div>
            <div class="address-msg">{{item.salePrice}}</div>
            <div class="telephone">{{item.sub_title}}</div>
            <!-- <div class="telephone">{{item.productImageBig}}</div> -->
            <!-- <div class="telephone">{{item.productImageSmall[0]}}</div> -->
            <!-- <div class="telephone">{{item.height}}</div>
            <div class="telephone">{{item.width}}</div> -->
            <!-- <div class="telephone">{{item.url[0]}}</div> -->
            <div class="operation">
              <!-- <a href="javascript:;" @click="update(item)">修改</a> -->
              <a href="javascript:;" :data-id="item.id" @click="del(item._id,i)">删除</a>
            </div>
           
          </div>
        </div>
        
      </div>
    </y-shelf>
    <y-popup :open="popupOpen" @close='popupOpen=false' :title="popupTitle">
      <div slot="content" class="md" :data-id="msg.id">
        <div>
          <input type="text" placeholder="二手商品名称" v-model="msg.productName">
        </div>
        <div>
          <input type="number" placeholder="价格" v-model="msg.salePrice">
        </div>
        <div>
          <input type="text" placeholder="商品子名称" v-model="msg.sub_title">
        </div>
        <!-- <div>
          <input type="number" placeholder="stock" v-model="msg.stock">
        </div> -->
        <div>
          <input type="text" placeholder="产品大图" v-model="msg.productImageBig">
        </div>
        <div>
          <input type="text" placeholder="产品小图" v-model="msg.productImageSmall[0]">
        </div>
         <!-- <div>
          <input type="text" placeholder="产品信息" v-model="msg.productImageSmall">
        </div> -->
        <div>
          <input type="text" placeholder="产品高度" v-model="msg.productMsg.height">
        </div>
        <div>
          <input type="text" placeholder="产品宽度" v-model="msg.productMsg.width">
        </div>
        <div>
          <input type="text" placeholder="产品url" v-model="msg.productMsg.url[0]">
        </div>
        <y-button text='保存'
                  class="btn"
                  :classStyle="'main-btn'"
                  @btnClick="save({msg})">
        </y-button>
      </div>
    </y-popup>
  </div>
</template>
<script>
  import { productList, addressUpdate, productAdd, addressDel,productDel } from '/api/goods'
  import YButton from '/components/YButton'
  import YPopup from '/components/popup'
  import YShelf from '/components/shelf'
  export default {
    data () {
      return {
        addList: [],
        popupOpen: false,
        popupTitle: '管理收货地址',
        msg: {
          id: '',
          productImageSmall:[],
          productMsg:{
              url:[]
          }
        }
      }
    },
    computed: {
      btnHighlight () {
        let msg = this.msg
        return msg.userName && msg.tel && msg.streetName
      }
    },
    methods: {
      _addressList () {
        productList({pageSize:10000}).then(res => {
          let data = res.result.data
          if (data.length) {
            this.addList = data
            // this.addressId = res.result[0].addressId || '1'
          } else {
            this.addList = []
          }
        })
        //获取商品信息
      },
      _addressUpdate (params) {
        addressUpdate(params).then(res => {
          this._addressList()
        })
      },
      _addressAdd (params) {
        productAdd(params).then(res => {
          this._addressList()
        })
      },
      // 保存
      save (p) {
        if (p.addressId) {
          this._addressUpdate(p)
        } else {
          delete p.addressId
          this._addressAdd(p)
        }
        this.popupOpen = false
      },
      // 删除
      del (_id, i) {
        productDel({_id}).then(res => {
          if (res.status === '0') {
            this.addList.splice(i, 1)
          } else {
            alert('删除失败')
          }
        })
      },
      // 修改
      update (item) {
        this.popupOpen = true
         
        this.popupTitle = '新增商品'
        this.msg.userName = ''
        this.msg.tel = ''
        this.msg.streetName = ''
        this.msg.isDefault = false
        this.msg.addressId = ''
        
      }
    },
    created () {
      this._addressList()
    },
    components: {
      YButton,
      YPopup,
      YShelf
    }
  }
</script>
<style scoped lang="scss">
  .address-item {
    display: flex;
    align-items: center;
    height: 115px;
    text-align: center;
    .name {
      width: 106px;
    }
    .address-msg {
      flex: 1;
      width: 10px
    }
    .telephone {
      width: 160px;
    }
    .defalut {
      width: 80px;
      > a {
        text-align: center;
        /*display: none;*/
      }
    }
    .operation {
      width: 135px;
      a {
        padding: 10px 5px;
      }
    }
    &:hover {
      .defalut > a {
        display: block;
      }
    }
  }

  .address-item + .address-item {
    border-top: 1px solid #CFCFCF;
  }

  .defalut-address {
    color: #626262;
    display: block;
    pointer-events: none;
    cursor: default;
  }

  .md {
    > div {
      text-align: left;
      margin-bottom: 15px;
      > input {
        width: 100%;
        height: 28px;
        font-size: 18px;
        padding: 0px 20px;
        border: 1px solid #ccc;
        border-radius: 6px;
        box-shadow: 0 3px 5px -4px rgba(0, 0, 0, .4) inset, -1px 0 3px -2px rgba(0, 0, 0, .1) inset;
        line-height: 46px;
      }
    }
  }

  .btn {
    margin: 0;
    width: 100%;
    height: 50px;
    font-size: 14px;
    line-height: 48px
  }
</style>
