<template>
  <div>
    <Header></Header>
    <div class="content">
      <div class="leftMenu">
          <ul>
            <li>
              <router-link to="#">
                <div class="icon"></div>
                <router-link to="/myCollect" style="color:#000;font-weight:bold">我的订阅</router-link>
              </router-link>
            </li>
            <li>
              <router-link to="#">
                <div class="icon"></div>
                <router-link to="/myCollect">我收藏的清单</router-link>
              </router-link>
            </li>
          </ul>
      </div>
      <div class="rightWrap">
        <!-- header -->
        <div class="header">
          <div class="top">
            <p>我的订阅清单</p>
          </div>
          <div class="bottom">
            <img :src="avtar" alt="" class="avtar">
            <span class="name"> {{name}} </span>
          </div>
          <div class="bc_img"></div>
        </div>
        <p class="record"> {{data.length}}条记录</p>
        <!-- 记录 -->
        <div class="subscribe" v-show="data.length>0">
          <div class="sub_items" v-for="(item,index) in data" :key="index">
            <div class="left">
              <router-link to="#">
              <img :src="item[0].img" alt="" class="full">
              </router-link>
            </div>
            <div class="right">
              <span class="cancleSub" @click="cancleSub(item[0].id)">取消订阅</span>
              <router-link to="#" class="name">{{item[0].name}}</router-link>
              <p class="state">{{item[0].state}}</p>
              <p class="state">共{{item[0].all}}集</p>
            </div>
          </div>
        </div>
        <div class="none flexCen" v-show="data.length==0">
          暂无记录,快去订阅吧。
        </div>
        <!-- 最新推荐 -->
        <NewTuiJian/>
        <div class="footer">
          <Footer/>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
import axios from 'axios'
import Header from '@/components/common/header.vue';
import NewTuiJian from '@/components/common/newTuiJian.vue';
import Footer from '@/components/common/footer.vue';
export default {
  components:{
    Header,
    NewTuiJian,
    Footer
  },
  data(){
    return{
      data:'',
      avtar:'',
      name:'',
    }
  },
  methods:{
    getData(){
      let uid = localStorage.getItem('id')
      axios.get('/api/user/userSubscribe',{
        params:{
          uid:uid
        }
      })
      .then(res=>{
        this.data = res.data.data
        for(let i=0;i<this.data.length;i++){
          if(this.data[i][0].state == 1){
            this.data[i][0].state = '完结'
          }else{
            this.data[i][0].state = '连载'
          }
        }
      })
    },
    cancleSub(id){
      // 取消订阅
      axios.get('/api/movie/cancelSub',{
        params:{
          id:id
        }
      })
      .then(res=>{
        if(res.data.data.affectedRows == 1 ){
          this.getData()
          this.$notify.info({
            title: '取消成功',
            message: '已取消订阅',
            type: 'success'
          });
        }else{
          this.$notify.error({
            title: '取消失败',
            message: '发生错误'
          });
        }
      })
    }
  },
  mounted(){
    this.getData();
    this.name = localStorage.getItem('name');
    this.avtar = localStorage.getItem('avtar');
    window.scrollTo(0, 0)
  }
}
</script>
<style lang="less" scoped>
.clear{
  clear: both;
}
.content{
  width: 1000px;
  margin: 0 auto;
  margin-top: 80px;
}
.leftMenu{
  position: fixed;
  width: 166px;
  height: 509px;
  top: 80px;
  padding: 14px 0 0 14px;
  ul{
    padding: 0;
  }
  li{
    height: 30px;
    line-height: 30px;
    list-style: none;
    a{
      color:#888;
      text-decoration: none;
      &:hover{
        color: rgb(173, 37, 37);
      }
    }
  }
}
.rightWrap{
  height: 100%;
  width: 800px;
  float: right;
  position: relative;
  .header{
    height: 265px;
    width: 100%;
    position: relative;
    .top{
      height: 50%;
      width: 100%;
      background: url(http://oo9xy1zeh.bkt.clouddn.com/tiaomu.png)no-repeat;
      background-size: cover;
      position: relative;
      p{
        position: absolute;
        bottom: 10px;
        left: 220px;
        color:#fff;
        font-weight: bold;
        font-size: 26px;
      }
    }
    .bottom{
      height: 50%;
      width: 100%;
      background: #fff;
      position: relative;
      .avtar{
        width: 40px;
        height: 40px;
        border-radius: 50%;
        position: absolute;
        top: 20px;
        left: 220px;
      }
      .name{
        position: absolute;
        top: 30px;
        left: 280px;
        font-size: 16px;
        font-weight: bold;
      }
    }
    .bc_img{
      position: absolute;
      width: 162px;
      height: 162px;
      // border-radius: 50%;
      background: url(http://oo9xy1zeh.bkt.clouddn.com/wallhaven-644926.jpg)no-repeat -80px;
      background-size: cover;
      left: 33px;
      top: 66px;
    }
  }
  .record{
    height: 40px;
    line-height: 40px;
    color: #666;
    font-size: 14px;
  }
  .subscribe{
    width: 500px;
    border-radius: 4px;
    float: left;
    padding: 15px;
    background: #fff;
    .sub_items{
      width: 100%;
      height: 130px;
      border-bottom: 1px dashed #bbb;
      margin-bottom: 12px;
      &:last-child{
        margin-bottom: 0px;
      }
      .left{
        width: 17%;
        height: 120px;
        float: left;
      }
      .right{
        width: 83%;
        height: 120px;
        float: left;
        padding: 12px;
        box-sizing: border-box;
        position: relative;
        font-size: 16px;
        .cancleSub{
          position: absolute;
          right: 10px;
          top: 15px;
          font-size: 14px;
          cursor: pointer;
          &:hover{
            color:salmon;
          }
        }
        .name{
          display: block;
          color: #555;
          transition: all .25s;
          margin-bottom: 30px;
          &:hover{
            color:salmon;
          }
        }
        p{
          color: #777;
          font-size: 14px;
        }
      }
    }
  }
}
.footer{
  float: left;
  margin-top: 100px;
}
</style>

