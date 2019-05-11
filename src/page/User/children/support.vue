<template>
  <div>
    <y-shelf title="用户管理">
      <div slot="content">
        <div v-if="addList.length">
          <div class="address-item" v-for="(item,i) in addList" :key="i">
            <div class="name">{{item.userName}}</div>
            <div class="address-msg">{{item.avatar}}</div>
            <div class="telephone">{{item.name}}</div>
           
            <div class="operation">
              <a href="javascript:;" @click="update(item)">修改</a>
              <a href="javascript:;" :data-id="item.userId" @click="del(item.userId,i)">删除</a>
            </div>
          </div>
        </div>
      </div>
    </y-shelf>
      <y-popup :open="popupOpen" @close='popupOpen=false' :title="popupTitle">
      <div slot="content" class="md" :data-id="msg.userId">
        <div>
          <input type="text" placeholder="登陆名" v-model="msg.userName">
        </div>
        <div>
          <input type="text" placeholder="昵称" v-model="msg.name">
        </div>
        <div>
          <input type="text" placeholder="头像地址" v-model="msg.avatar">
        </div>
        
        
        <y-button text='修改'
                  class="btn"
                  :classStyle='main-btn'
                  @btnClick="save({userId:msg.userId,userName:msg.userName,avatar:msg.avatar,name:msg.name})">
        </y-button>
      </div>
    </y-popup>
  </div>
</template>
<script>
import { userInfoList,userInfo } from '/api/index'
  import YShelf from '/components/shelf'
   import YButton from '/components/YButton'
  import YPopup from '/components/popup'

  export default {
    data () {
      return {
        addList: [],
        popupOpen: false,
        popupTitle: '管理收货地址',
        msg: {
          addressId: '',
          userName: '',
          tel: '',
          streetName: '',
          isDefault: false
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
        userInfoList().then(res => {
          let data = res.result
          if (data.length) {
            this.addList = res.result
            this.addressId = res.result[0].addressId || '1'
          } else {
            this.addList = []
          }
        })
      },
      _addressUpdate (params) {
        userInfo(params).then(res => {
          this._addressList()
        })
      },
     
     
      // 保存
      save (p) {
          p.act='update'
        if (p.userId) {
          this._addressUpdate(p)
        }
        this.popupOpen = false
      },
      // 删除
      del (userId, i) {
        userInfo({userId:userId,act:'delete'}).then(res => {
          if (res.status === 0) {
            this.addList.splice(i, 1)
          } else {
            alert('删除失败')
          }
        })
      },
      // 修改
      update (item) {
        this.popupOpen = true
    
        this.popupTitle = '修改用户信息'
        this.msg.userName = item.userName
        this.msg.name = item.name
        this.msg.avatar = item.avatar
        this.msg.userId = item.userId  
        
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
        height: 50px;
        font-size: 18px;
        padding: 10px 20px;
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

