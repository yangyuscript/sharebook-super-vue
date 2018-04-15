<template>
  <div class="container">
    <Form ref="formInline" :model="formInline" :rules="ruleInline" inline>
      <FormItem prop="account">
        <Input type="text" v-model="formInline.account" placeholder="帖子名称">
        <Icon type="bookmark" slot="prepend"></Icon>
        </Input>
      </FormItem>
      <FormItem>
        <Button type="primary" @click="handleSubmit('formInline')">查找</Button>
      </FormItem>
      <FormItem>
        <Button type="primary" @click="modal1 = true">新添帖子</Button>
      </FormItem>
    </Form>
    <Table border :columns="columns7" :data="data6"></Table>
    <Page :total="total" :page-size="10" @on-change="changePage"></Page>

    <Modal
      v-model="modal1"
      title="新添帖子"
      width="900"
      @on-ok="ok('formItem2')"
    >
      <Form ref="formItem2" :model="formItem2" :rules="ruleItem2" :label-width="80">
        <FormItem label="标题" prop="title">
          <Input v-model="formItem2.title" placeholder=""></Input>
        </FormItem>
        <FormItem label="内容" prop="descri">
          <quill-editor v-model="formItem2.descri" ref="VueQuillEditor"
                        :content="content"
                        @change="onEditorChange($event)">
          </quill-editor>
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
    name: 'ManagePosts',
    data () {
      return {
        total: '',
        condi: '',
        modal1: false,
        modal2: false,
        content: '',
        currDelBtId: 0,
        currDelBtIndex: -1,
        formInline: {
          account: ''
        },
        formItem2: {
          title: '',
          descri: ''
        },
        ruleItem2: {
          title: [{
            required: true,
            message: '请填写帖子标题,字数不能超过50',
            trigger: 'blur',
            max: 50
          }],
          descri: [{
            required: true,
            message: '请填写帖子内容',
            trigger: 'blur',
            max: 10000
          }]
        },
        columns7: [
          {
            title: '编号',
            key: 'postid',
            render: (h, params) => {
              return h('div', [
                h('Icon', {
                  props: {
                    type: 'bookmark'
                  }
                }),
                h('strong', params.row.postid)
              ]);
            }
          },
          {
            title: '标题',
            key: 'title'
          },
          {
            title: '发布时间',
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
                    type: 'primary',
                    size: 'small'
                  },
                  style: {
                    marginRight: '5px'
                  },
                  on: {
                    click: () => {
                      this.show(params.index)
                    }
                  }
                }, '查看详情'),
                h('Button', {
                  props: {
                    type: 'error',
                    size: 'small'
                  },
                  on: {
                    click: () => {
                      this.modal2=true
                      this.currDelBtId=params.row.postid
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
      show (index) {
        this.$Modal.info({
          title: '帖子详情',
          width: 800,
          content: `标题：${this.data6[index].title}<br>内容：${this.data6[index].content}`
        })
      },
      remove (index) {
        console.log("haha")
        this.data6.splice(index, 1);
      },
      request (currentPage){
        var that=this
        this.$http.post(that.GLOBAL.serverPath + '/super/getAllPosts',
          {
            title: that.formInline.account,
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
          that.data6=res.data.posts
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
            that.$http.post(that.GLOBAL.serverPath + '/super/addPost',
              {
                title: that.formItem2.title,
                content: that.formItem2.descri
              },
              {
                emulateJSON: true,
                headers: {
                  'x-access-token': window.localStorage.getItem('x-access-token')
                }
              }
            ).then(function (res) {
              console.log(res.data.status)
              if(res.data.status=='ok'){
                that.$Message.success('新增成功')
                that.formInline.account=''
                that.formItem2.title=''
                that.formItem2.descri=''
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
        this.$http.post(that.GLOBAL.serverPath + '/super/delPost',
          {
            postid: that.currDelBtId,
          },
          {
            emulateJSON: true,
            headers: {
              'x-access-token': window.localStorage.getItem('x-access-token')
            }
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
      },
      onEditorChange({editor,html,text}){
        // 富文本编辑器，文本改变时，设置字段值
        console.log(editor,html,text)
        this.content = html
      }
    }
  }
</script>
