<template>
  <div class="container">
    <Form ref="formInline" :model="formInline" :rules="ruleInline" inline>
      <FormItem prop="account">
        <Input type="text" v-model="formInline.account" placeholder="书籍种类名称">
        <Icon type="bookmark" slot="prepend"></Icon>
        </Input>
      </FormItem>
      <FormItem>
        <Button type="primary" @click="handleSubmit('formInline')">查找</Button>
      </FormItem>
      <FormItem>
        <Button type="primary" @click="modal1 = true">新添书籍种类</Button>
      </FormItem>
    </Form>
    <Table border :columns="columns7" :data="data6"></Table>
    <Page :total="total" :page-size="10" @on-change="changePage"></Page>

    <Modal
      v-model="modal1"
      title="新添书籍种类"
      @on-ok="ok('formItem2')"
    >
      <Form ref="formItem2" :model="formItem2" :rules="ruleItem2" :label-width="80">
        <FormItem label="种类名称" prop="account">
          <Input v-model="formItem2.account" placeholder=""></Input>
        </FormItem>
      </Form>
    </Modal>
    <Modal
      v-model="modal2"
      title="提示"
      @on-ok="ok2"
    >
      <p>是否确定删除？</p>
    </Modal>
  </div>
</template>
<script type="es6">
  export default {
    name: 'ManageBookTypes',
    data () {
      return {
        total: '',
        condi: '',
        modal1: false,
        modal2: false,
        currDelBtId: 0,
        currDelBtIndex: -1,
        formInline: {
          account: ''
        },
        formItem2: {
          account: '',
        },
        ruleItem2: {
          account: [{
            required: true,
            message: '请填写书籍种类名称！',
            trigger: 'blur',
            max: 10
          }]
        },
        columns7: [
          {
            title: '编号',
            key: 'btid',
            render: (h, params) => {
              return h('div', [
                h('Icon', {
                  props: {
                    type: 'bookmark'
                  }
                }),
                h('strong', params.row.btid)
              ]);
            }
          },
          {
            title: '名称',
            key: 'name'
          },
          {
            title: '创建时间',
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
                      this.modal2=true
                      this.currDelBtId=params.row.btid
                      this.currDelBtIndex=params.index
                    }
                  }
                }, '删除')
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
        console.log("haha")
        this.data6.splice(index, 1);
      },
      request (currentPage){
        var that=this
        this.$http.post(that.GLOBAL.serverPath + '/super/getAllBookTypes',
          {
            name: that.formInline.account,
            currentPage: currentPage
          },
          {
            emulateJSON: true
          }
        ).then(function (res) {
          console.log(res.data.pageInfo)
          that.total=res.data.pageInfo.total
          that.data6=res.data.bookTypes
        }).catch((e) => {
          this.$Message.fail('网络有误！')
        })
      },
      changePage: function(page){
        this.request(page)
      },
      ok (name) {
        var that=this
        this.$refs[name].validate((valid) => {
          if (valid) {
            that.$http.post(that.GLOBAL.serverPath + '/super/addBookType',
              {
                btname: that.formItem2.account,
              },
              {
                emulateJSON: true
              }
            ).then(function (res) {
              console.log(res.data.status)
              if(res.data.status=='ok'){
                that.$Message.success('新增成功')
                that.formInline.account=''
                that.formItem2.account=''
                that.request(1)
              }else{
                that.$Message.error('新增失败')
              }
            }).catch((e) => {
              that.$Message.fail('网络有误！')
            })
          }
        })
      },
      ok2(){
        console.log("..............................")
        var that=this
        this.$http.post(that.GLOBAL.serverPath + '/super/delBookType',
          {
            btid: that.currDelBtId,
          },
          {
            emulateJSON: true
          }
        ).then(function (res) {
          console.log(res.data.pageInfo)
          if(res.data.status == 'ok'){
            that.$Message.success('删除成功')
            console.log("........."+that.currDelBtIndex)
            that.data6.splice(that.currDelBtIndex, 1);
          }
        }).catch((e) => {
          this.$Message.fail('网络有误！')
        })
      }
    }
  }
</script>
