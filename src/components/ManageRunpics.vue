<template>
  <div>
    <!--滚屏管理区域-->
    <Row>
      <Col span="12" v-for="(item,index) in data7" :key="index">
      <div style="background:#eee; padding:20px">
        <Card :bordered="false">
          <p style="height:34px;" slot="title">第{{index+1}}个滚屏<Button @click='modify(index+"")' type="primary" shape="circle">修改</Button></p>
          <img :src=item.url style="width:400px;height:200px;"/>
        </Card>
      </div>
      </Col>
    </Row>
    <Modal
      v-model="modal1"
      title="修改微信小程序端首页滚屏"
      width="900"
      @on-ok="ok('formItem2')"
      @on-cancel="cancel">
      <Form ref="formItem2" :model="formItem2" :rules="ruleItem2" :label-width="80">
        <FormItem label="图片网络地址" prop="url">
          <Input v-model="formItem2.url" placeholder="图片网络地址"></Input>
        </FormItem>
        <FormItem label="详细内容" prop="description">
          <quill-editor v-model="formItem2.description" ref="VueQuillEditor"
                        :content="content"
                        @change="onEditorChange($event)">
          </quill-editor>
        </FormItem>
      </Form>
    </Modal>
  </div>
</template>
<script type="es6">
  export default {
    name: 'ManageRunpics',
    data() {
      return {
        modal1: false,
        currModifyIndex: 0,
        data7: [],
        content: '',
        formItem2: {
          url: '',
          description: ''
        },
        ruleItem2: {
          url: [{
            required: true,
            message: '请填写滚屏网络地址',
            trigger: 'blur',
            max: 1000
          }],
          description: [{
            required: true,
            message: '请填写内容',
            trigger: 'blur',
            max: 10000
          }]
        },
      }
    },
    mounted() {
      this.request()
    },
    methods: {
      request() {
        var that = this
        this.$http.get(that.GLOBAL.serverPath + '/index/getRunpics',
          {},
          {
            emulateJSON: true,
          }
        ).then(function (res) {
          that.data7 = res.data.runpics
        }).catch((e) => {
          this.$Message.fail('网络有误！')
        })
      },
      modify (index){
        var $index=parseInt(index)
        this.modal1=true
        this.currModifyIndex=$index
        this.formItem2.url=this.data7[$index].url
        this.formItem2.description=this.data7[$index].description
      },
      ok (name) {
        var that=this
        this.$refs[name].validate((valid) => {
          if (valid) {
            that.$http.post(that.GLOBAL.serverPath + '/super/updateRunpic',
              {
                url: that.formItem2.url,
                description: that.formItem2.description,
                rid: that.data7[that.currModifyIndex].rid
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
                that.$Message.success('修改滚屏成功')
                that.formItem2.url=''
                that.formItem2.description=''
                that.request()
              }else{
                that.$Message.error('修改滚屏失败')
              }
            }).catch((e) => {
              that.$Message.fail('网络有误！')
            })
          }
        })
      },
      cancel () {
        this.$Message.info('取消修改');
      },
      onEditorChange({editor,html,text}){
        // 富文本编辑器，文本改变时，设置字段值
        console.log(editor,html,text)
        this.content = html
      }
    }
  }
</script>

