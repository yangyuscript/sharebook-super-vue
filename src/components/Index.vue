<template>
  <div class="layout">
    <Row type="flex">
      <Col span="5" class="layout-menu-left">
      <Menu active-name="2-1" theme="dark" width="auto" :open-names="['2']">
        <div class="layout-logo-left">
          <h2 style="color:white;">流书管理平台</h2>
        </div>
        <Submenu name="1">
          <template slot="title">
            <Icon type="ios-navigate"></Icon>
            后台管理
          </template>
          <MenuItem name="1-1"><span @click="go_ManageUsers">用户管理</span></MenuItem>
          <MenuItem name="1-2"><span @click="go_ManageBooks">书籍管理</span></MenuItem>
          <MenuItem name="1-3"><span @click="go_ManageBookTypes">书籍种类管理</span></MenuItem>
          <MenuItem name="1-4"><span @click="go_ManagePosts">帖子管理</span></MenuItem>
          <MenuItem name="1-5"><span @click="go_ManageNotices">通知管理</span></MenuItem>
        </Submenu>
        <Submenu name="2">
          <template slot="title">
            <Icon type="ios-keypad"></Icon>
            数据监控
          </template>
          <MenuItem name="2-1"><span @click="go_ManageContent">平台数据监管</span></MenuItem>
          <MenuItem name="2-2"><span @click="go_ManageRunpics">微信端滚屏</span></MenuItem>
        </Submenu>
      </Menu>
      </Col>
      <Col span="19">
      <div class="layout-header"></div>
      <div class="layout-breadcrumb">
        <Breadcrumb>
          <BreadcrumbItem href="#">{{one_nav}}</BreadcrumbItem>
          <BreadcrumbItem href="#">{{two_nav}}</BreadcrumbItem>
          <BreadcrumbItem>{{three_nav}}</BreadcrumbItem>
        </Breadcrumb>
      </div>
      <div class="layout-content">
        <div class="layout-content-main">
          <template id="ManageUsers"></template>
          <template id="ManageBooks"></template>
          <template id="ManageBookTypes"></template>
          <template id="ManagePosts"></template>
          <template id="ManageNotices"></template>
          <component :is="currentView"></component>
        </div>
      </div>
      <div class="layout-copy">
        2017-2018 &copy; 南京邮电大学校园共享书籍--流书
      </div>
      </Col>
    </Row>
  </div>
</template>
<script>
  import ManageUsers from '../components/ManageUsers.vue'
  import ManageBooks from '../components/ManageBooks.vue'
  import ManageBookTypes from '../components/ManageBookTypes.vue'
  import ManagePosts from '../components/ManagePosts.vue'
  import ManageNotices from '../components/ManageNotices.vue'
  import ManageContent from '../components/ManageContent.vue'
  import ManageRunpics from '../components/ManageRunpics.vue'
  import Button from 'iview/src/components/button/button'
  export default {
    name: 'Index',
    data () {
      return {
        msg: 'haha',
        one_nav: '主页',
        two_nav: '后台管理',
        three_nav: '平台数据监管',
        currentView: 'ManageContent'
      }
    },
    methods: {
      go_ManageContent () {
        this.one_nav = '主页'
        this.two_nav = '后台管理'
        this.three_nav = '平台数据监管'
        this.currentView = 'ManageContent'
      },
      go_ManageRunpics () {
        this.one_nav = '主页'
        this.two_nav = '后台管理'
        this.three_nav = '微信端滚屏'
        this.currentView = 'ManageRunpics'
      },
      go_ManageUsers () {
        this.one_nav = '主页'
        this.two_nav = '后台管理'
        this.three_nav = '用户管理'
        this.currentView = 'ManageUsers'
      },
      go_ManageBooks () {
        this.one_nav = '主页'
        this.two_nav = '后台管理'
        this.three_nav = '书籍管理'
        this.currentView = 'ManageBooks'
      },
      go_ManageBookTypes () {
        this.one_nav = '主页'
        this.two_nav = '后台管理'
        this.three_nav = '书籍种类管理'
        this.currentView = 'ManageBookTypes'
      },
      go_ManagePosts () {
        this.one_nav = '主页'
        this.two_nav = '后台管理'
        this.three_nav = '帖子管理'
        this.currentView = 'ManagePosts'
      },
      go_ManageNotices () {
        this.one_nav = '主页'
        this.two_nav = '后台管理'
        this.three_nav = '通知管理'
        this.currentView = 'ManageNotices'
      }
    },
    components: {
      Button,
      ManageContent: ManageContent,
      ManageRunpics: ManageRunpics,
      ManageUsers: ManageUsers,
      ManageBooks: ManageBooks,
      ManageBookTypes: ManageBookTypes,
      ManagePosts: ManagePosts,
      ManageNotices: ManageNotices
    }
  }
</script>
<style scoped>
  .layout{
    border: 1px solid #d7dde4;
    background: #f5f7f9;
    position: relative;
    margin-top:-60px;
  }
  .layout-breadcrumb{
    padding: 10px 15px 0;
    text-align:left;
  }
  .layout-content{
    min-height: 200px;
    margin: 15px;
    overflow: hidden;
    background: #fff;
    border-radius: 4px;
  }
  .layout-content-main{
    padding: 10px;
  }
  .layout-copy{
    text-align: center;
    padding: 10px 0 20px;
    color: #9ea7b4;
  }
  .layout-menu-left{
    background: #464c5b;
  }
  .layout-header{
    height: 60px;
    background: #fff;
    box-shadow: 0 1px 1px rgba(0,0,0,.1);
  }
  .layout-logo-left{
    width: 90%;
    height: 30px;
    background: #5b6270;
    border-radius: 3px;
    margin: 15px auto;
  }
  Button{
    color:white;
  }
</style>
