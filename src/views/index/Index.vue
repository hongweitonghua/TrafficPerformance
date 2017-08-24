<template>
    <section class="chart">
        <el-row> 
            <el-col class="chart-container">
                <div class="chart-header">
                    <el-select v-model="timeRange" placeholder="请选择" @change="TimeRangeChange"  >
                        <el-option key="上月" label="上月" value="上月"></el-option>
                        <el-option key="本季" label="本季" value="本季"></el-option>
                        <el-option key="本年" label="本年" value="本年"></el-option>
                    </el-select>
                </div>
                <div class="chart-header2">
                    统计期：{{ countDate }}
                </div>
            </el-col>
        </el-row
        <el-row> 
        <!-- 能源类型图 -->
            <el-col :span="11" class="chart-container">                
                <div id="energyTypePie" style="width:100%; height:400px;" class="chart-content"></div>
            </el-col>
            <!-- 能源分时图 -->
             <el-col :span="13" class="chart-container">                
                <div id="barChart" style="width:100%; height:400px;" class="chart-content"></div>
            </el-col>
        </el-row>
    </section>
</template>

<script>
    import {getCookie,delCookie,setCookie} from '../../common/js/Cookie.js';
    var year = (new Date).getFullYear().toString();
    var xAisMon = [year+'-01',year+'-02',year+'-03',year+'-04',year+'-05',year+'-06',
            year+'-07',year+'-08',year+'-09',year+'-10',year+'-11',year+'-12']
    var dataForEngAll=[];
  //  var dataForMon=[];
    var dataFor
    var MonengMap = {};

    var energyTypePie;
    var barChart;

    var beforTimeRange = '';
    var beforeMonth = '';
    var requestData = 
    {
    } 

    var optionPi = {
        title:{
            text: '能源结构图',
            x: 'center'
        },
        tooltip : {
            trigger: 'item',
            formatter: "{a} <br/>{b} : {c} ({d}%)"
        },
        legend: {
            orient: 'vertical',
            left: 'left',
            data: []
        },
        toolbox: {
            show : true,
            feature : {
                mark : {show: true},                 
                dataView : {show: true, readOnly: false},
                saveAsImage : {show: true},
            },
            right:'4%'
        },
        series : [
            {
                name: '能耗',
                type: 'pie',
                radius : '70%',
                center: ['50%', '55%'],
                data:[],
                
            }
        ]

    };
    var optionMon={
        title:{
            text: '能源分时图',
            left:'center'
        },
        grid: {
            left: '10%',
            right: '15%',
        },
        dataZoom: [
            {
                id: 'dataZoomX',
                type: 'slider',
                xAxisIndex: [0],
                filterMode: 'filter'
            }
        ],
        tooltip: {
            trigger: 'axis',
            axisPointer : {            
                type : 'shadow'        
            }
        },
        legend: {
            data:[],
            left:'left'
        },
        toolbox: {
            show : true,
            feature : {
            mark : {show: true},
            dataView : {readOnly:false},
            saveAsImage : {show: true}              
            },
            right:'4%'
        },
        xAxis: {
            data: ['1月','2月','3月','4月','5月','6月','7月','8月','9月','10月','11月','12月'],
            name:'月份',
            nameGap:'10'
        },
        yAxis: {
            name:'能耗(万吨标准煤)',
            /*nameLocation:'middle',
            nameGap:'40'*/
        },
        series : [
            {
                //name:'单耗',
                type:'bar',
                data:[]
            }
        ]
    };

    function setData(res){
        
        var engerData = {};
        var monthData = new Object();

        var eng_all_for_PI = []; //饼图填充数据
        var month_all = new Array();//周转量
        MonengMap = new Object();
        res.engTypOther.forEach(function(element){
            element.engTypMo.forEach(function(e2){
                var t = monthData[e2.type];
                if(!t) t = [0,0];
                t[0] += e2.typDatOfAllEng;
                t[1] += e2.typDatOfAllLen;
                monthData[e2.type] = t;

                // cal engall
                var t = engerData[element.baseTyp];
                if(!t) t = [0,0];
                t[0] += e2.typDatOfAllEng;
                t[1] += e2.typDatOfAllLen;
                engerData[element.baseTyp] = t; 

                if(!MonengMap[element.baseTyp])
                    MonengMap[element.baseTyp] = {};
                if(!MonengMap[element.baseTyp][e2.type])
                    MonengMap[element.baseTyp][e2.type]=[0,0];
                
                var t = MonengMap[element.baseTyp][e2.type];
                t[0] += e2.typDatOfAllEng;
               // t[1] += e2.typDatOfAllLen;
                MonengMap[element.baseTyp][e2.type] = t;
            });
        });
        MonengMap['所有能源'] = monthData;

        //准备能源饼图数据
        res.xs[1].forEach(function(e1){
            var t = engerData[e1];
            if(t)
            {
                eng_all_for_PI.push({name:e1,value:(t[0]/10000).toFixed(2)})
            }else{
            }
        });


        dataForEngAll.splice(0,dataForEngAll.length);
        dataForEngAll.push(res.xs[1]);
        dataForEngAll.push(eng_all_for_PI);


        setDataWhenEngChg('所有能源','#3398DB');

    }

    function setDataWhenEngChg(engType,barColor){
        var tmpEngDatas = [];
        xAisMon.forEach(function(e1){
            var t = MonengMap[engType][e1];
            if(t){
                tmpEngDatas.push((t[0]/10000).toFixed(2));
            }else{
                tmpEngDatas.push(0);
            }
        });
        optionMon.series[0].data = tmpEngDatas;
        optionMon.legend.data = [engType];
        optionMon.series[0].name = engType;
        optionMon.color = [barColor];
        barChart.clear();
        barChart.setOption(optionMon);
    }
   
     
    

    import echarts from 'echarts'
 //   import {ajaxAddress} from '../../common/js/constant.js'
  //  var currentEnergy = -1;
    export default {
        data() {
            return {
                timeRange: '上月',
                countDate: ''
            }
        },
       
        methods: {

            initRequestData(requestData){
                var date = new Date();
                var year = date.getFullYear();
                var month = date.getMonth();
                if(month==0){
                    year = year -1;
                    month = 12;
                }else{
                    if(month>=1 && month<=9)
                        month = '0'+month;
                }
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
                beforeMonth = requestData.timeRange;
            },

            getDataFromService(requestData){
                console.log(requestData);
                //requestData['timeRange'] = '2013-09-01:2013-10-31'; 
                var _this = this;
                energyTypePie.showLoading({text:'加载中'});
                barChart.showLoading({text:'加载中'});
                $.get(this.Constant.ajaxAddress+this.Constant.indexAjax,requestData).
                done(function (res){
                    console.log(JSON.stringify(res));
                    energyTypePie.hideLoading();
                    barChart.hideLoading();
                    if(res.errCode==30){//data ok
                        setData(res);
                      //  optionPi.legend.data = dataForEngAll[0];
                        optionPi.series[0].data = dataForEngAll[1];
                        energyTypePie.clear();
                        energyTypePie.setOption(optionPi);

                        //optionMon.xAxis.data = dataForMon[0];
                       /* optionMon.legend.data=[dataForMon[0]];
                        optionMon.color = ['#3398DB'];
                        optionMon.series[0].name = dataForMon[0];
                        optionMon.series[0].data = dataForMon[1];
                        barChart.clear();
                        barChart.setOption(optionMon); */
                    }else if(res.errCode==31){ // data err
                        window.log('unknow err');
                    }else if(res.errCode==44){ // auth 
                        _this.$router.push('/login');
                    }
                });

                
            },
            TimeRangeChange(tr){
                if(!tr||tr== '')
                    return ;
                if(tr=='上月'){
                    /*var date = new Date;
                    var year = date.getFullYear().toString();
                    var month = (date.getMonth()+1).toString();
                    var day = (date.getDate()-1).toString();
                    
                    if(month>=1&&month<=9)
                        month = '0' + month;
                    
                    if(day>=1&&day<=9)
                        day = '0' + day;*/
                    requestData['timeRange'] = beforeMonth;
                    this.getDataFromService(requestData);
                    beforTimeRange = tr;
                }
                if(tr=='本季'){
                    var date = new Date;
                    var year = date.getFullYear();
                    var month = date.getMonth()+1;
                    var startMon = 3*Math.ceil(month/3)-2;
                    if(month == startMon){
                        this.$message({
                            showClose: true,
                            message: '本季度还未有统计数据上报',
                            type: 'warning',
                            duration:2500
                        });
                    }else{
                        month = month - 1;
                        if(month>=1&&month<=9)
                            month = '0' + month;
                        requestData['timeRange'] = year+'-'+startMon.toString()+'-01:'
                                               +year+'-'+month.toString()+'-31';
                        if(month == startMon){
                            this.countDate = year+'年'+month+'月';
                        }else{
                            this.countDate = year+'年'+startMon+'月 至 '+year+'年'+month+'月';
                        }
                        this.getDataFromService(requestData);
                        beforTimeRange = tr;
                        
                    }
                    
                }
                if(tr=='本年'){
                    var date = new Date;
                    var year = date.getFullYear().toString();
                    var month = date.getMonth()+1;
                    if(month==1){
                        this.$message({
                            showClose: true,
                            message: '本年度还未有统计数据上报',
                            type: 'warning',
                            duration:2500
                        });
                    }else{
                        month = month -1;
                        if(month>=1&&month<=9)
                            month = '0' + month;
                        requestData['timeRange'] = year+'-01-01:'+year+'-'+month+'-31';
                        this.countDate = year+'年01月 至 '+year+'年'+month+'月';
                        this.getDataFromService(requestData);
                        beforTimeRange = tr;
                    }        
                }
            },
            EngTypeChange(){
                energyTypePie.on('click',function(params){
                    //  alert('click pie');
                    var engType = params.name;
                    var barColor = params.color;
                    /*var tmpEngDatas = [];
                    xAisMon.forEach(function(e1){
                        var t = MonengMap[engType][e1];
                        if(t){
                            tmpEngDatas.push((t[0]));
                        }else{
                            tmpEngDatas.push(0);
                        }
                    });
                    optionMon.series[0].data = tmpEngDatas;
                    optionMon.legend.data = [engType];
                    optionMon.series[0].name = engType;
                    optionMon.color = [barColor];
                    barChart.setOption(optionMon);*/
                    setDataWhenEngChg(engType,barColor);
                });
                barChart.on('dblclick',function(params){
                    setDataWhenEngChg('所有能源','#3398DB');
                });
            }
        },
        mounted: function () {
            energyTypePie = echarts.init(document.getElementById('energyTypePie'));
            barChart = echarts.init(document.getElementById('barChart'));
            energyTypePie.setOption(optionPi);
            barChart.setOption(optionMon);
            this.initRequestData(requestData);
            this.getDataFromService(requestData);
            this.EngTypeChange();
            
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
