<template>
  <div class="content" v-title data-title="课程详情">
    <div class="detail-title clearFix">
      <div class="title-left">
        <img src="http://localhost:8080/static/img/qx.png">
      </div>
      <div class="title-right">
        <h1>{{detail.title}}</h1>
        <p class="purchase"><i></i>{{detail.purchase}}人购买</p>
        <div class="title-content">
          <p>{{detail.info}}</p>
        </div>
        <p class="price">￥{{detail.price}}</p>
        <!-- 购买过后去掉class，内容变为 已参加 -->
        <button class="join" v-show="!detail.myCourse" @click="goToBuy(detail.id,detail.title)">立即参加</button>
        <button v-show="detail.myCourse">已参加</button>
      </div>
    </div>
    <div class="course-nav">
      <ul class="clearFix">
        <li v-for="(list,index) in navList" :class="{active:index==num}" @click="navChange(index)">{{list}}</li>
      </ul>
    </div>
    <div>
      <ul class="course-detail">
        <li v-show="num==0">
          <div class="nav-title">介绍</div>
          <div class="info">
            <p>{{detail.info}}</p>
          </div>
        </li>
        <li v-show="num==1">
          <div class="nav-title">目录</div>
          <div class="list">
            <ul>
              <li class="clearFix" v-for="doc in detail.file">
                <div class="left">
                  <span>{{doc.fileTilte}}</span>
                </div>
                <div class="right">
                  <!-- 已经学习过的添加class -->
                  <a :href="doc.docSrc" :class="[doc.state?'already':'']">{{doc.fileContent}}</a>
                </div>
              </li>
            </ul>
          </div>
        </li>
        <li v-show="num==2">
          <div class="nav-title">考试</div>
          <div class="exam-nav clearFix">
            <div class="exam">
              <div class="title">
                <p>模拟考试</p>
              </div>
              <div class="time-day">
                <p class="time">考试次数：<span>{{detail.moniTestNum}}</span>/{{detail.moniTestTotalNum}}</p>
                <p class="day">剩余天数：<span>{{detail.examDeadLine}}</span>天</p>
              </div>
              <div class="btn">
                <!-- 模拟次数用完之后，添加class -->
                <button :class="{disabled:detail.moniTestNum==detail.moniTestTotalNum}" @click="goToTest">立即考试</button>
              </div>
            </div>
            <div class="exam">
              <div class="title">
                <p>正式考试</p>
              </div>
              <div class="time-day">
                <p class="day">剩余天数：<span>{{detail.examDeadLine}}</span>天</p>
              </div>
              <div class="btn">
                <button @click="goToTest">立即考试</button>
              </div>
            </div>
          </div>
        </li>
      </ul>
    </div>
  </div>
</template>
<script>
import { getCourse } from 'api/course'
import { ERR_OK } from 'api/config'
import { mapGetters } from 'vuex'
export default {
  data() {
    return {
      detail: [],
      navList: ['介绍', '目录', '考试'],
      num: 0
    }
  },
  methods: {
    goToBuy: function(id,title) {
      if (this.isLogin) {
        this.$router.push({path:'/courseCenter/courseDetail/payment',query:{'id':id,'title':title}});
      } else {
        this.$router.push('/login');
      }
    },
    navChange: function(index) {
      this.num = index;
    },
    goToTest: function() {
      this.$router.push('/mineExam/paper');
    },
    _getData: function() {
      getCourse().then((res) => {
        if (res.code === ERR_OK) {
          var data = res.courseList;
          data.forEach((item) => {            
            if (item.id == this.$route.query.id) {
              this.detail = item;
              if (!this.isLogin) {
                this.detail.myCourse = false;
              }
              if (!this.detail.myCourse) {
                this.navList = ['介绍'];
              }
            }

          })
        }
      })
    }
  },
  computed: {
    ...mapGetters(['isLogin'])
  },
  created() {
    this._getData();
  }
}

</script>
<style scoped>
@import 'courseDetail.css'

</style>
