<template>
    <section class="chart">
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
            <el-col :xs="24" :sm="24" :md="12" :lg="12" class="chart-container">                
                <div id="energyPieChart" style="width:100%; height:400px;" class="chart-content"></div>
            </el-col>
            <el-col :xs="24" :sm="24" :md="12" :lg="12" class="chart-container">                
                <div id="companyChart" style="width:100%; height:400px;" class="chart-content"></div>
            </el-col>   
        </el-row>
        <!--<el-row>
            <el-col  class="chart-container">                
                <div id="distanceShipChart" style="width:100%; height:400px;" class="chart-content"></div>
            </el-col>  
        </el-row>-->
        <el-row> 
            <el-col  class="chart-container">                
                <div id="energyTonnageChart" style="width:100%; height:400px;" class="chart-content"></div>
            </el-col>
              
        </el-row>
        <el-row> 
            <el-col class="chart-container">                
                <div id="seaShipChart" style="width:100%; height:400px;" class="chart-content"></div>
            </el-col>
        </el-row>   

        <el-row>
            <el-col class="chart-container">
                <div class="chart-header">
                    <el-date-picker
                        v-model="year"
                        align="right"
                        type="year"
                        placeholder="选择年"
                        @change="selectYearMonth"
                        :picker-options="pickerOptions2">
                    </el-date-picker>
                </div>
                <div id="energyByYearChart" style="width:100%; height:400px;" class="chart-content"></div>
            </el-col>
        </el-row>   
    </section>
</template>

<script>
    import echarts from 'echarts'
    import {getCookie,delCookie,setCookie} from '../common/js/Cookie.js';
    var dataForMoth = [];//年度图表
    var dataForScalePer = [];//各规模企业类型单耗
    var dataForEngTog = [];//能源吨位
    var dataforTogShip =[];//吨位船舶类型
    var dataForEngAll = [];//各能源饼图
    var k=3; //标志

    var energyPieChart;
    var energyTonnageChart;
    var energyByYearChart;
    var companyChart;
    var seaShipChart;


    var _year = (new Date).getFullYear().toString();
    
    var requestData = {};

    var colors = ['#5793f3', '#d14a61'];
    var option = {
        color:colors,
        title:{
            text:'年度单耗、使用能耗关系图',
            left:'center'

        },
        tooltip: {
            trigger: 'axis',
            axisPointer: {
                type: 'cross'
            }
        },
        toolbox: {
                show : true,
                feature : {
                    mark : {show: true},
                    dataView : {readOnly:false},
                    saveAsImage : {show: true}
                
                },
                right:'3%'
            },
        legend: {
            data:['月使用能耗','单位能耗'],
            top : 20
        },
        xAxis: [
            {
                type: 'category',
                axisPointer: {
                    type: 'shadow'
                },
                name:'月份',
                nameGap:'20',
                data: ['1月','2月','3月','4月','5月','6月','7月','8月','9月','10月','11月','12月']
            }
        ],
        yAxis: [
            {
                type: 'value',
                name: '月使用能耗(万吨标准煤)',
                axisLine: {
                    lineStyle: {
                        color: '#5793f3'
                    }
                },
                axisLabel: {
                    formatter: '{value} '
                }
            },
            {
                type: 'value',
                name: '单位能耗(万吨标准煤/亿吨公里)',
                nameGap : 35,
                nameLocation:'middle',
                axisLine: {
                    lineStyle: {
                        color: '#d14a61'
                    }
                },
                axisLabel: {
                    formatter: '{value} '
                }
            }
        ],
        series: [
            {
                name:'月使用能耗',
                type:'line'
            },
            {
                name:'单位能耗',
                type:'bar',
                yAxisIndex: 1
            }
        ]
    };
    var optionPi={
        title:{
            text: '内河运输能源结构图',
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
                radius : '80%',
                center: ['50%', '60%'],
                data:[],
            }
        ]

    };
    var optionScale={
        title:{
            text: '不同规模企业单位能耗柱状图',
            left:'center'
        },
        tooltip: {
            trigger: 'axis',
            axisPointer : {            
                type : 'shadow'        
            }
        },
        legend: {
            data:[]
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
            data: [],
            name:'企业规模',
            nameGap:'2'
        },
        yAxis: {
            name:'单位能耗(万吨标煤/亿吨公里)',
            nameLocation:'middle',
            nameGap:'40'
        },
        series : [
            {
                name:'单耗',
                type:'bar',
                barMaxWidth:'60%',
                data:[]
            }
        ]
    };
    var optionEngTog={
        title: {
            text: '不同燃料类型不同吨位船舶单位能耗柱状图',
            left:'center'
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
            top : 30
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
                name:'燃料类型',
                nameGap:'2'
            }
        ],
        yAxis : [
            {
                type : 'value',
                nameLocation : 'middle',
                name : '单位能耗(单位：万吨标准煤/亿吨公里)',
                nameGap : 35
            }
        ],
        series : []
    };
    var optionTogShip={
        title: {
            text: '不同吨位不同类型船舶单位能耗柱状图',
            left:'center'
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
            top : 30
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
                name:'吨位',
                nameGap:'2'
            }
        ],
        yAxis : [
            {
                type : 'value',
                nameLocation : 'middle',
                name : '单位能耗(单位：万吨标准煤/亿吨公里)',
                nameGap : 35
            }
        ],
        series : []
    };

    function setData(res){
        //console.leg(res);
        var monthData = new Object();
        var engerData = {};
        var scaleData={};
        var engTogMap={};
        var togShipMap={};
        var month_all = new Array();//周转量
        var month_per = new Array();// eng_cl_e / eng_cl_l ;

        var eng_all_for_PI = []; //饼图填充数据
        var engTogSeries = [];
        var togShipSeries = [];
        var eng_per_for_scale =[];

        var xAisMon = [_year+'-01',_year+'-02',_year+'-03',_year+'-04',_year+'-05',_year+'-06',
            _year+'-07',_year+'-08',_year+'-09',_year+'-10',_year+'-11',_year+'-12']

        //遍历计算
        res.engTypOther.forEach(function(element) {
            //month data
            element.engTypMo.forEach(function(e2){
                var t = monthData[e2.type];
                if(!t) t = [0,0];
                t[0] += e2.typDatOfAllEng;
                t[1] += e2.typDatOfAllLen;
                monthData[e2.type] = t;
            });

            //engTogMap
            element.engTypWs.forEach(function(e2){
                if(!engTogMap[e2.type])
                    engTogMap[e2.type] = {};
                if(!engTogMap[e2.type][element.baseTyp])
                        engTogMap[e2.type][element.baseTyp] = [0,0];
                
                var t = engTogMap[e2.type][element.baseTyp];
                t[0] += e2.typDatOfAllEng;
                t[1] += e2.typDatOfAllLen;
                engTogMap[e2.type][element.baseTyp] = t;

                // cal engall
                var t = engerData[element.baseTyp];
                if(!t) t = [0,0];
                t[0] += e2.typDatOfAllEng;
                t[1] += e2.typDatOfAllLen;
                engerData[element.baseTyp] = t;                   
            });

        });
        //togShipMap
        res.weiTypOther.forEach(function(element){
            element.weiTypSt.forEach(function(e2){
                if(!togShipMap[e2.type])
                    togShipMap[e2.type] = {};
                if(!togShipMap[e2.type][element.baseTyp])
                    togShipMap[e2.type][element.baseTyp] = [0,0];
                
                var t = togShipMap[e2.type][element.baseTyp];
                t[0] += e2.typDatOfAllEng;
                t[1] += e2.typDatOfAllLen; 
                togShipMap[e2.type][element.baseTyp] = t;
            });
        });

        //scaleData
        res.entTypOther.forEach(function(element){
            var t = scaleData[element.baseTyp];
            if(!t) t = [0,0];
            t[0] += element.baseTypDatOfAllEng;
            t[1] += element.baseTypDatOfAllLen;
            scaleData[element.baseTyp] = t;
        });

        //准备能源饼图数据
        res.xs[1].forEach(function(e1){
            var t = engerData[e1];
            if(t)
            {
                eng_all_for_PI.push({name:e1,value:(t[0]/10000).toFixed(2)})
               // eng_per.push((t[0]/t[1]).toFixed(2))
            }else{
                //eng_all_for_PI.push({name:e1,value:0})
               // eng_per.push(0)
            }
        });
        //准备燃料吨位数据
        res.xs[2].forEach(function(i){
            var tmpEngDatas = [];
            if(!engTogMap[i])
            {
                var tmpSeriseObj = {
                            name:i,
                            type:'bar',
                            data:[]
                    };
                engTogSeries.push(tmpSeriseObj);
                
            }else
            {
                res.xs[1].forEach(function(e1){
                    var t = engTogMap[i][e1];
                    if(t)
                    {
                        tmpEngDatas.push((t[0]/t[1]).toFixed(2))
                    }else{
                        //eng_all_for_PI.push({name:e1,value:0})
                        tmpEngDatas.push(0);
                    }
                });
                var tmpSeriseObj = {
                                        name:i,
                                        type:'bar',
                                        data:tmpEngDatas
                                };
                engTogSeries.push(tmpSeriseObj);
            }
        });

        //准备企业规模数据
        res.xs[3].forEach(function(e1){
            var t = scaleData[e1];
            if(t){
                eng_per_for_scale.push((t[0]/t[1]).toFixed(2));
            }else{
                eng_per_for_scale.push(0);
            }
        });

        //准备吨位船舶类型数据
        res.xs[4].forEach(function(i){
            var tmpEngDatas =[];
            if(!togShipMap[i]){
                var tmpSeriseObj = {
                        name:i,
                        type:'bar',
                        data:[]
                };
                togShipSeries.push(tmpSeriseObj);
            }else{
                res.xs[2].forEach(function(e1){
                    var t = togShipMap[i][e1];
                    if(t){
                        tmpEngDatas.push((t[0]/t[1]).toFixed(2));
                    }else{
                        tmpEngDatas.push(0);
                    }
                });
                var tmpSeriseObj = {
                    name:i,
                    type:'bar',
                    data:tmpEngDatas
                };
                togShipSeries.push(tmpSeriseObj);
            }
        });


        

        if( k == 1 ||k==3 )
        {
            dataForEngAll.splice(0,dataForEngAll.length);
            dataForEngAll.push(res.xs[1]);
            dataForEngAll.push(eng_all_for_PI);

            dataForScalePer.splice(0,dataForScalePer.length);
            dataForScalePer.push(res.xs[3]);
            dataForScalePer.push(eng_per_for_scale);

            dataForEngTog.splice(0,dataForEngTog.length);
            dataForEngTog.push(res.xs[2]);
            dataForEngTog.push(res.xs[1]);
            dataForEngTog.push(engTogSeries);

            dataforTogShip.splice(0,dataforTogShip.length);
            dataforTogShip.push(res.xs[4]);
            dataforTogShip.push(res.xs[2]);
            dataforTogShip.push(togShipSeries);
        }
        // engMonth
        xAisMon.forEach(function(e1){
            var t = monthData[e1];
            if(t) 
            {
                month_all.push((t[0]/10000).toFixed(2));
                month_per.push((t[0]/t[1]).toFixed(2));
            }else
            {
                month_all.push(0);
                month_per.push(0);
            }
        });
        if( k == 2)
        {
            dataForMoth.splice(0,dataForMoth.length);
            dataForMoth.push(res.xs[0]);
            dataForMoth.push(month_all);
            dataForMoth.push(month_per); 
        }      
    }

    export default {
        data() {
            return {
                year:'',
                countDate: '',
                beginDate:'',
                endDate:'',
                pickerOptions0: {
                    disabledDate(time) {
                        return time.getTime() > Date.now() - 8.64e7;
                    }
                },
                pickerOptions1: {
                    disabledDate(time) {
                        return time.getTime() > Date.now() - 8.64e7;
                    }
                },
                pickerOptions2: {
                    disabledDate(time) {
                        if(new Date().getMonth==0)
                            return time.getTime() > Date.now() - 8.64e7;
                        else
                            return time.getTime() > Date.now() + 8.64e7;
                    }
                }
            }
        },
        methods: {
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
                if(k==1||k==3){
                    energyPieChart.showLoading({text:'加载中'});
                    energyTonnageChart.showLoading({text:'加载中'});
                    companyChart.showLoading({text:'加载中'});
                    seaShipChart.showLoading({text:'加载中'});
                }
                if(k==2){
                    energyByYearChart.showLoading({text:'加载中'});
                }
                $.get(this.Constant.ajaxAddress+this.Constant.rivertranAjax,requestData).
                done(function (res){
                    if(res.errCode==30){//data ok
                        setData(res);
                        if(k==1||k==3)
                        {

                            energyPieChart.hideLoading();
                            energyTonnageChart.hideLoading();
                            companyChart.hideLoading();
                            seaShipChart.hideLoading();
                            
                            optionPi.series[0].data = dataForEngAll[1];
                            energyPieChart.clear();
                            energyPieChart.setOption(optionPi);

                            optionEngTog.legend.data = dataForEngTog[0];
                            optionEngTog.xAxis[0].data = dataForEngTog[1];
                            optionEngTog.series = dataForEngTog[2];
                            energyTonnageChart.clear();
                            energyTonnageChart.setOption(optionEngTog);

                            optionScale.xAxis.data = dataForScalePer[0];
                            optionScale.series[0].data = dataForScalePer[1];
                            companyChart.clear();
                            companyChart.setOption(optionScale);

                            optionTogShip.legend.data = dataforTogShip[0];
                            optionTogShip.xAxis[0].data = dataforTogShip[1];
                            optionTogShip.series = dataforTogShip[2];
                            seaShipChart.clear();
                            seaShipChart.setOption(optionTogShip);

                            

                        }
                        if(k ==2){
                            energyByYearChart.hideLoading();

                            //option.xAxis[0].data =  dataForMoth[0];
                            option.series[1].data = dataForMoth[2];
                            option.series[0].data = dataForMoth[1];
                            energyByYearChart.clear();
                            energyByYearChart.setOption(option);
                        }
                    }else if(res.errCode==31){ // data err
                        window.log('unknow err');
                    }else if(res.errCode==44){ // auth 
                        _this.$router.push('/login');
                    }
  
                });
                
            },
            selectOther(){
                k = 1;
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
            selectYearMonth(y){
                k =2;

                if(!y||y=='')
                    return ;
                
                _year = y;
                var date = new Date();
                var year = date.getFullYear();
                if(year==y){
                    var month = date.getMonth().toString();
                    y = y+'-01-01:'+y+'-'+month+'-31';
                }else{
                    y = y +'-01-01:'+y+'-12-31';
                }
                requestData['timeRange']=y;
                
                this.getDataFromService(requestData);
            }     
        },
        mounted: function () {
            

            energyPieChart = echarts.init(document.getElementById('energyPieChart'));
            companyChart = echarts.init(document.getElementById('companyChart'));
            energyTonnageChart = echarts.init(document.getElementById('energyTonnageChart'));
            seaShipChart = echarts.init(document.getElementById('seaShipChart'));
            energyByYearChart = echarts.init(document.getElementById('energyByYearChart'));
            energyPieChart.setOption(optionPi);
            companyChart.setOption(optionScale);
            energyTonnageChart.setOption(optionEngTog);
            seaShipChart.setOption(optionTogShip);
            energyByYearChart.setOption(option);
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
        padding: 15px 15px;
    }

    .el-row {
        margin-bottom: 15px;
        &:last-child {
        margin-bottom: 0;
        }
    }
</style>