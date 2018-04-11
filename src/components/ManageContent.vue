<template>
  <div>
    <Row>
      <Col span="12">
      <div style="background:#eee; padding:20px">
      <Card :bordered="false">
        <p slot="title">No border title</p>
        <div id="pie" style="width: 400px;height: 200px;"></div>
      </Card>
      </div>
      </Col>
      <Col span="12">
      <div style="background:#eee; padding:20px">
      <Card :bordered="false">
        <p slot="title">No border title</p>
        <div id="pie2" style="width: 400px;height: 200px;"></div>
      </Card>
      </div>
      </Col>
    </Row>
    <Row>
      <Col span="12">
      <div style="background:#eee; padding:20px">
      <Card :bordered="false">
        <p slot="title">No border title</p>
        <div id="pie3" style="width: 400px;height: 200px;"></div>
      </Card>
      </div>
      </Col>
      <Col span="12">
      <div style="background:#eee; padding:20px">
      <Card :bordered="false">
        <p slot="title">No border title</p>
        <div id="pie4" style="width: 400px;height: 200px;"></div>
      </Card>
      </div>
      </Col>
    </Row>
  </div>
</template>
<script type="es6">
  import echarts from 'echarts'
  export default {
    name: 'ManageContent',
    data() {
      return {
        charts: '',
        opinion:['直接访问','邮件营销','联盟广告','视频广告','搜索引擎'],
        opinionData:[
          {value:335, name:'直接访问'},
          {value:310, name:'邮件营销'},
          {value:234, name:'联盟广告'},
          {value:135, name:'视频广告'},
          {value:1548, name:'搜索引擎'}
        ],
        total: '',
        condi: '',
        modal1: false,
        formInline: {
          account: ''
        },
        columns7: [
          {
            title: '书籍id',
            key: 'bid',
            render: (h, params) => {
              return h('div', [
                h('Icon', {
                  props: {
                    type: 'ios-book'
                  }
                }),
                h('strong', params.row.bid)
              ]);
            }
          },
          {
            title: '书名',
            key: 'bookname'
          },
          {
            title: '封面',
            key: 'bookpic',
            render: (h, params) => {
              return h('div', [
                h('img', {
                  attrs: {
                    src: this.GLOBAL.serverPath + '/' + params.row.bookpic
                  },
                  style: {
                    width: '40px',
                    height: '40px',
                    background: '#87d068'
                  }
                })
              ])
            }
          },
          {
            title: '分类',
            key: 'booktype'
          },
          {
            title: '发布时间',
            key: 'time'
          },
          {
            title: '状态',
            key: 'bookcondi',
            render: (h, params) => {
              return h('span', {
                style: {
                  color: params.row.condicolor
                }
              }, params.row.bookcondi)
            }
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
                      this.remove(params.index)
                    }
                  }
                }, '屏蔽')
              ]);
            }
          }
        ],
        data6: [],
        data7: []
      }
    },
    mounted() {
      this.request(1)
      this.$nextTick(function() {
        this.drawPie('pie')
        this.drawPie('pie2')
        this.drawPie('pie3')
        this.drawPie('pie4')
      })
    },
    methods: {
      drawPie(id){
        this.charts = echarts.init(document.getElementById(id))
        this.charts.setOption({
          tooltip: {
            trigger: 'item',
            formatter: '{a}<br/>{b}:{c} ({d}%)'
          },
          legend: {
            orient: 'vertical',
            x: 'left',
            data:this.opinion
          },
          series: [
            {
              name:'访问来源',
              type:'pie',
              radius:['50%','70%'],
              avoidLabelOverlap: false,
              label: {
                normal: {
                  show: false,
                  position: 'center'
                },
                emphasis: {
                  show: true,
                  textStyle: {
                    fontSize: '30',
                    fontWeight: 'blod'
                  }
                }
              },
              labelLine: {
                normal: {
                  show: false
                }
              },
              data:this.opinionData
            }
          ]
        })
      },
      handleSubmit(account) {
        this.request(1)
      },
      show(index) {
        this.$Modal.info({
          title: '书籍详情',
          content: `发布者：${this.data7[index].user.nickname}<br>书籍描述：${this.data7[index].description}`
        })
      },
      remove(index) {
        this.data6.splice(index, 1);
      },
      request(currentPage) {
        var that = this
        this.$http.post(that.GLOBAL.serverPath + '/super/getBookInfoByPage',
          {
            bookname: that.formInline.account,
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
          that.total = res.data.pageInfo.total
          that.data6 = []
          that.data7 = res.data.books
          that.data7.forEach((e) => {
            let obj = {}
            obj.bid = e.bid
            obj.bookname = e.bookname
            obj.bookpic = e.bookpic
            obj.booktype = e.bookType.name
            obj.time = e.time
            if (e.condi === 1) {
              //屏蔽
              obj.bookcondi = '屏蔽'
              obj.condicolor = 'red'
            } else {
              obj.bookcondi = '正常'
              obj.condicolor = 'black'
            }
            that.data6.push(obj)
          })

        }).catch((e) => {
          this.$Message.fail('网络有误！')
        })
      },
      changePage: function (page) {
        this.request(page)
      }
    }
  }
</script>

