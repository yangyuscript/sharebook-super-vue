<template>
  <div>
    <Row>
      <Col span="12">
      <div style="background:#eee; padding:20px">
      <Card :bordered="false">
        <p slot="title">注册用户男女比例</p>
        <div id="pie" style="width: 400px;height: 200px;"></div>
      </Card>
      </div>
      </Col>
      <Col span="12">
      <div style="background:#eee; padding:20px">
      <Card :bordered="false">
        <p slot="title">主要用户比例</p>
        <div id="pie2" style="width: 400px;height: 200px;"></div>
      </Card>
      </div>
      </Col>
    </Row>
    <Row>
      <Col span="12">
      <div style="background:#eee; padding:20px">
      <Card :bordered="false">
        <p slot="title">书籍种类分布</p>
        <div id="pie3" style="width: 400px;height: 200px;"></div>
      </Card>
      </div>
      </Col>
      <Col span="12">
      <div style="background:#eee; padding:20px">
      <Card :bordered="false">
        <p slot="title">实时数据</p>
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
        //第一个图数据 注册用户男女比例
        opinion:['男','女'],
        opinionData:[
          {value:335, name:'男'},
          {value:310, name:'女'}
        ],
        //第一个图数据 主要用户比例
        opinion2:['男','女'],
        opinionData2:[
          {value:335, name:'男'},
          {value:310, name:'女'}
        ],
        //第一个图数据 书籍种类分布
        opinion3:['男','女'],
        opinionData3:[
          {value:335, name:'男'},
          {value:310, name:'女'}
        ],
        //第一个图数据 实时数据
        opinion4:['男','女'],
        opinionData4:[
          {value:335, name:'男'},
          {value:310, name:'女'}
        ],

        data6: [],
      }
    },
    mounted() {
      this.request()
    },
    methods: {
      drawPie(id){
        var $opinion
        var $opinionData
        console.log("fuck")
        if(id==''){
          $opinion=this.opinion
          console.log($opinion)
          $opinionData=this.opinionData
        }else if(id=='2'){
          $opinion=this.opinion2
          $opinionData=this.opinionData2
        }else if(id=='3'){
          $opinion=this.opinion3
          $opinionData=this.opinionData3
        }else{
          $opinion=this.opinion4
          $opinionData=this.opinionData4
        }
        this.charts = echarts.init(document.getElementById('pie'+id))
        this.charts.setOption({
          tooltip: {
            trigger: 'item',
            formatter: '{a}<br/>{b}:{c} ({d}%)'
          },
          legend: {
            orient: 'vertical',
            x: 'left',
            data:$opinion
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
              data:$opinionData
            }
          ]
        })
      },
      request() {
        var that = this
        this.$http.post(that.GLOBAL.serverPath + '/super/getData',
          {},
          {
            emulateJSON: true,
            headers: {
              'x-access-token': window.localStorage.getItem('x-access-token')
            }
          }
        ).then(function (res) {
          if(res.data.status=='ok'){
            console.log(res.data)
            var obj=[]
            res.data.bigData.opinionData.forEach((e)=>{
              if(e.name=="1"){
                e.name="男"
                obj.push("男")
              }else{
                e.name="女"
                obj.push("女")
              }
            })
            that.opinionData=res.data.bigData.opinionData
            that.opinion=obj

            var obj2=[]
            res.data.bigData.opinionData2.forEach((e)=>{
              obj2.push(e.name)
            })
            that.opinionData2=res.data.bigData.opinionData2
            that.opinion2=obj2
            console.log(that.opinionData)

            var obj3=[]
            res.data.bigData.opinionData3.forEach((e)=>{
              obj3.push(e.name)
            })
            that.opinionData3=res.data.bigData.opinionData3
            that.opinion3=obj3

            this.$nextTick(function() {
              this.drawPie('')
              this.drawPie('2')
              this.drawPie('3')
              this.drawPie('4')
            })
          }
        }).catch((e) => {
          this.$Message.fail('网络有误！')
        })
      }
    }
  }
</script>

