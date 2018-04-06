<template>
  <div class="container">
    <Form ref="formInline" :model="formInline" :rules="ruleInline" inline>
      <FormItem prop="account">
        <Input type="text" v-model="formInline.account" placeholder="昵称">
        <Icon type="ios-person-outline" slot="prepend"></Icon>
        </Input>
      </FormItem>
      <FormItem>
        <Button type="primary" @click="handleSubmit('formInline')">查找</Button>
      </FormItem>
    </Form>
    <Table border :columns="columns7" :data="data6"></Table>
    <Page :total="total" :page-size="10" @on-change="changePage"></Page>
  </div>
</template>
<script type="es6">
  export default {
    name: 'ManageUsers',
    data () {
      return {
        total: '',
        condi: '',
        modal1: false,
        formInline: {
          account: ''
        },
        formItem2: {
          account: '',
          name: '',
          sex: '男',
          condi: '0'
        },
        ruleItem2: {
          account: [{
            required: true,
            message: '请填写账号！',
            trigger: 'blur'
          }],
          name: [{
            required: true,
            message: '请填写学生姓名',
            trigger: 'blur'
          }]
        },
        columns7: [
          {
            title: 'email',
            key: 'email',
            render: (h, params) => {
              return h('div', [
                h('Icon', {
                  props: {
                    type: 'person'
                  }
                }),
                h('strong', params.row.email)
              ]);
            }
          },
          {
            title: '昵称',
            key: 'nickname'
          },
          {
            title: '头像',
            key: 'head',
            render: (h,params) =>{
              return h('div',[
                h('Avatar',{
                  attrs :{
                    src: params.row.head
                  },
                  style: {
                    width:'40px',
                    height:'40px',
                    background: '#87d068'
                  }
                })
              ])
            }
          },
          {
            title: '积分',
            key: 'credit'
          },
          {
            title: '性别',
            key: 'gender'
          },
          {
            title: '城市',
            key: 'city'
          },
          {
            title: '省份',
            key: 'province'
          },
          {
            title: '国家',
            key: 'country'
          },
          {
            title: '时间',
            key: 'time'
          },
          {
            title: '操作',
            key: 'action',
            width: 150,
            align: 'center',
            render: (h, params) => {
              return h('div', [
                h('Button', {
                  props: {
                    type: 'error',
                    size: 'small'
                  },
                  on: {
                    click: () => {
                      this.remove(params.index)
                    }
                  }
                }, '封号')
              ]);
            }
          }
        ],
        data6: []
      }
    },
    mounted(){
      this.request(1);
    },
    methods: {
      handleSubmit(account) {
        this.request(1)
      },
      remove (index) {
        this.data6.splice(index, 1);
      },
      request (currentPage){
        var that=this
        this.$http.post(that.GLOBAL.serverPath + '/super/getUserInfoByPage',
          {
            nickname: that.formInline.account,
            condi: 3,
            time: null,
            currentPage: currentPage
          },
          {
            emulateJSON: true,
            headers: {
              'x-access-token': window.localStorage.getItem('x-access-token')
            }
          }
        ).then(function (res) {
          console.log(res.data.pageInfo)
          that.total=res.data.pageInfo.total
          that.data6=res.data.users
        }).catch((e) => {
          this.$Message.fail('网络有误！')
        })
      },
      changePage: function(page){
        this.request(page)
      }
    }
  }
</script>

