<template>
  <section class = "chart">
      <el-row> 
            <el-col class="chart-container">
                <div class="chart-header">
                    <el-date-picker
                        v-model="endDate"
                        type="month"
                        placeholder="结束年月"
                        @change="selectOther"
                        :picker-options="pickerOptions0">
                    </el-date-picker>
                </div>
                <div class="chart-header">
                    <el-date-picker
                        v-model="beginDate"
                        type="month"
                        placeholder="起始年月"
                        @change="selectOther"
                        :picker-options="pickerOptions1">
                    </el-date-picker>
                </div>
                
                <div class="chart-header2">
                    统计期：{{ countDate }}
                </div>
            </el-col>
        </el-row>
      <el-row>
            <el-col class="chart-container">
                <div id="allTypChart"style="width:100%; height:400px;" class="chart-content"></div>
            </el-col>
      </el-row>    
  </section>
</template>

<script>
import {getCookie,delCookie,setCookie} from '../common/js/Cookie.js';
var dataForTraEng = [];//运输类型燃料
var allTypChart;

var beforTimeRange = '';

var requestData = {};

var optionTraEng = {
    title: {
        text: '',
        //left:'center'
    },
    dataZoom: [
        {
            id: 'dataZoomX',
            type: 'slider',
            xAxisIndex: [0],
            filterMode: 'filter'
        }
    ],

    legend: {
        data:[],
        top : 10
    },
    tooltip : {
        trigger: 'axis',
        axisPointer : {            // 坐标轴指示器，坐标轴触发有效
            type : 'shadow'        // 默认为直线，可选为：'line' | 'shadow'
        }
    },
    toolbox: {
        show : true,
        feature : {
            mark : {show: true},
            magicType : {show: true, type: ['line', 'bar']},
            dataView : {readOnly:false},
            saveAsImage : {show: true}
        },
        right:'3%'
    },
    xAxis : [
        {
            type : 'category',
            data : [],
            name:'运输类型',
            nameGap:5
        }
    ],
    yAxis : [
        {
            type : 'value',
            //nameLocation : 'middle',
            name : '单位运距能耗(单位：吨标准煤/公里)',
            //nameGap : 35
        }
    ],
    series : []
};

function setData(res){
    console.log(res)
    var traEngMap={};

    var traEngSeries=[];

    res.traTypOther.forEach(function(element){ 
        element.traTypEt.forEach(function(e2){
            if(!traEngMap[e2.type])
                traEngMap[e2.type] ={};
            if(!traEngMap[e2.type][element.baseTyp])
                traEngMap[e2.type][element.baseTyp] =[0,0];
            var t = traEngMap[e2.type][element.baseTyp];
            t[0] += e2.typDatOfAllEng;
            t[1] += e2.typDatOfAllLen;
            traEngMap[e2.type][element.baseTyp] =t;
        });
    });
    res.xs[1].forEach(function(element){
        var tmpEngDatas = [];
        if(!traEngMap[element]){
            var tmpSeriseObj = {
                name:element,
                type:'bar',
                data:[]
            };
            traEngSeries.push(tmpSeriseObj);
        }else{
            res.xs[0].forEach(function(e2){
                var t = traEngMap[element][e2];
                if(t){
                    tmpEngDatas.push((t[0]/t[1]).toFixed(2))
                }else{
                    tmpEngDatas.push(0);
                }
            });
            var tmpSeriseObj={
                name:element,
                type:'bar',
                data:tmpEngDatas
            };
            traEngSeries.push(tmpSeriseObj);
        }
    });

    dataForTraEng.splice(0,dataForTraEng.length);
    dataForTraEng.push(res.xs[1]);
    dataForTraEng.push(res.xs[0]);
    dataForTraEng.push(traEngSeries);
};

export default {
    data(){
      return {
            countDate: '',
            beginDate:'',
            endDate:'',
            pickerOptions0: {
                disabledDate(time) {
                    if(time.getFullYear()>(new Date()).getFullYear())
                            return true;
                        if(time.getFullYear()==(new Date()).getFullYear())
                            return time.getMonth() >= (new Date()).getMonth();
                        else
                            return false;
                }
            },
            pickerOptions1: {
                disabledDate(time) {
                    if(time.getFullYear()>(new Date()).getFullYear())
                            return true;
                        if(time.getFullYear()==(new Date()).getFullYear())
                            return time.getMonth() >= (new Date()).getMonth();
                        else
                            return false;  
                }
            }
      }
    },
    methods:{
        initRequestData(requestData){
            var date = new Date;
            var year = date.getFullYear().toString();
            var month = date.getMonth();
            if(month>=1 && month<=9)
                month = '0'+month;
            var token = getCookie('token');
            var userInfo = JSON.parse(getCookie('userInfo'));
            requestData.token = token;
            requestData.username = userInfo.name;
            if(userInfo.roleName!=null && userInfo.roleName!="")
                requestData.roleName = userInfo.roleName;
            requestData.roleType = userInfo.roleType;
            if(userInfo.place1!=null && userInfo.place1!="")
                requestData.place1 =userInfo.place1;
            if(userInfo.place2!=null && userInfo.place2!="")
                requestData.place2 = userInfo.place2;          
            requestData.timeRange = year+'-'+month+'-01:'+year+'-'+month+'-31';

            this.countDate = year+'年'+month+'月';
        },
        getDataFromService(requestData){
            var _this = this;
            allTypChart.showLoading({text:'加载中'});
            $.get(this.Constant.ajaxAddress+this.Constant.perdisengAjax,requestData).
            done(function (res){
                console.log(JSON.stringify(res));
                    allTypChart.hideLoading();
                    if(res.errCode==30){//data ok
                    setData(res);
                    optionTraEng.legend.data = dataForTraEng[0];
                    optionTraEng.xAxis[0].data = dataForTraEng[1];
                    optionTraEng.series = dataForTraEng[2];
                    allTypChart.clear();
                    allTypChart.setOption(optionTraEng);
                }else if(res.errCode==31){ // data err
                    window.log('unknow err');
                }else if(res.errCode==44){ // auth 
                    _this.$router.push('/login');
                }                

            });
            
        },
        selectOther(){  
            if(this.beginDate!='' && this.endDate!=''){
                if(this.beginDate > this.endDate){
                    this.$message({
                        showClose: true,
                        message: '起始年月不能大于结束年月',
                        type: 'warning',
                        duration:2500
                    });
                    return;
                }
                var by = this.beginDate.getFullYear();
                var bm = this.beginDate.getMonth()+1;
                if(bm>=1 && bm <=9)
                    bm = '0'+bm;
                if(this.beginDate == this.endDate){
                    requestData['timeRange'] = by + '-' + bm +'-01:' + by + '-' +bm + '-31';
                    this.countDate = by+'年'+bm+'月';
                }else{
                    var ey = this.endDate.getFullYear();
                    var em = this.endDate.getMonth()+1;
                    if(em>=1 && em <=9)
                        em = '0'+em;
                    requestData['timeRange'] = by + '-' + bm +'-01:' + ey + '-' + em + '-31';
                    this.countDate = by+'年'+bm+'月 至 '+ey+'年'+em+'月';
                }
                this.getDataFromService(requestData);
            }
        },
    },
    mounted:function(){
        allTypChart = echarts.init(document.getElementById('allTypChart'));
        allTypChart.setOption(optionTraEng);
        this.initRequestData(requestData);
        this.getDataFromService(requestData);
        
    },
    updated: function () {
        console.log("update");
    }
}
</script>

<style scoped lang="scss">
    .chart {
        width: 100%;
        float: left;
        .chart-container{
             background-color: #F2F2F2; 
             border-radius: 8px;
            .chart-header{
                float: right;
                margin-right: 20px;
                position: relative;
            }
            .chart-header2{
                float: left;
                font-weight:500;
                margin-left: 20px;
                top:10px;
                position: relative;
            }
            .chart-content{
                overflow: hidden;
            }
            
        }
        
    }
    /*.chart div {
        height: 400px;
        float: left;
    }*/

    .el-col {
        padding: 20px 20px;
    }

    .el-row {
        margin-bottom: 15px;
        &:last-child {
        margin-bottom: 0;
        }
    }
</style>




