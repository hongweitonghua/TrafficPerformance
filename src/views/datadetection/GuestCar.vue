<template>
<section class ="chart">
    <el-row>
        <el-col  class="chart-container" >
            <div class="chart-header" >
                <el-input placeholder="请输入市名/企业经营许可证号/车牌号" v-model="inputCode" style="width:450px" >
                    <el-select v-model="inputRank" slot="prepend" placeholder="请选择监测项" @change="selectInputRank">
                        <el-option label="车辆" value="车辆"></el-option>
                        <el-option label="企业" value="企业"></el-option>
                        <el-option label="地市" value="地市"></el-option>
                    </el-select>
                    <el-button slot="append" icon="search" @click="getData"></el-button>
                </el-input>
            </div>
            <div class="chart-header">
                <el-date-picker
                    v-model="datTimRange"
                    type="date"
                    placeholder="请选择日期"
                    @change="selectDate"
                    :picker-options="pickerOptions0">
                </el-date-picker>
            </div>
        </el-col>
        
    </el-row>
    <el-row>
        <el-col class = "chart-container"> 
            <div div id="relTimeChart"style="width:100%; height:400px;" class="chart-content"></div>
        </el-col>
    </el-row>

    <el-row>
        <el-col class = "chart-container"> 
            <div div id="relTimeChartCo"style="width:100%; height:400px;" class="chart-content"></div>
        </el-col>
    </el-row>

</section>  
</template>

<script>

var titleName = '数据监测'
var titleNameCo = '数据监测'
var tranTypeA = '道路客运';

var relTimeData = [];//油气柱状图数据
var relTimeDataCo = [];//标煤曲线图数据

var relTimeChart;//油气柱状图
var relTimeChartCo;//标煤曲线图

var selectDs = [];


var requestData = 
{
    // username:'zwp',
    // roleName:'enterprice',
    // roleType:'R_TRA',
    // place1:'杭州',
    // place2:'江干区',
    // timeRange:'2017-01-01 00:01:01&2017-01-01 23:59:59',

    // tranType:tranTypeA,
    // // carId:null,
    // // shipId:null,
    // // cityType:null,
    // // companyId:null,

    // token:null

}


var option = {
    title: {
        text: titleName
    },
    tooltip: {
        axisPointer: {
            type: 'shadow'
        },
        trigger: 'axis'
    },
    legend: {
        data: [],
        align: 'left'
    },
    toolbox: {
        // y: 'bottom',
        feature: {
            dataZoom: {
                yAxisIndex: 'none'
            },
            restore: {},
            dataView: {},
            saveAsImage: {}
        },
        right:'20%'
    },
    xAxis: {
        name:'时间',
        data: [],
        silent: false,
        splitLine: {
            show: false
        }
    },
    yAxis: [
        {
            type: 'value',
            name: '油耗（升/百公里）',
            axisLine: {
                lineStyle: {
                    color: '#c23531'
                }
            },
            axisLabel: {
                formatter: '{value} '
            }
        },
        {
            type: 'value',
            name: '电耗（度/百公里）',
            axisLine: {
                lineStyle: {
                    color: '#2f4554'
                }
            },
            axisLabel: {
                formatter: '{value} '
            }
        }
    ],
    
    dataZoom: [
        {
            show: true,
            realtime: true,
            start: 0
        }
    ],
    
    series: [],
    animationEasing: 'elasticOut',
    animationDelayUpdate: function (idx) {
        return idx ;
    }
};

var optionCo = {
    tooltip: {
        
        trigger: 'axis',
        position: function (pt) {
            return [pt[0], '10%'];
        }
    },
    title: {
        left: 'left',
        text: titleNameCo,
    },
    legend: {
        data: [],
        align: 'left'
    },
    toolbox: {
        feature: {
            dataZoom: {
                yAxisIndex: 'none'
            },
            restore: {},
            dataView: {},
            saveAsImage: {}
        },
        right:'20%'
    },
    xAxis: {
        type: 'category',
        boundaryGap: false,
        data: []
    },
    yAxis: [
        {
            type: 'value',
            name: '油耗（吨标准煤/百公里）',
            axisLine: {
                lineStyle: {
                    color: '#c23531'
                }
            },
            axisLabel: {
                formatter: '{value} '
            }
        },
        {
            type: 'value',
            name: '电耗（吨标准煤/百公里）',
            axisLine: {
                lineStyle: {
                    color: '#2f4554'
                }
            },
            axisLabel: {
                formatter: '{value} '
            }
        }
    ],
    dataZoom: [
        {
            show: true,
            realtime: true,
            start: 0
        }
    ],
    series: [
        {
            name:'模拟数据',
            type:'line',
            smooth:true,
            symbol: 'none',
            sampling: 'average',
            itemStyle: {
                normal: {
                    color: 'rgb(255, 70, 131)'
                }
            },
            data: []
        }
    ]
};

export default {
    data(){
            return {
                datTimRange:(new Date()).toLocaleDateString(),
                inputRank:'',
                inputCode:'',
                pickerOptions0:{
                    disabledDate(time) {
                        if((new Date()).getHours()==0)
                            return time.getTime() > (Date.now()-60*60*1000);
                        else
                            return time.getTime() > Date.now();
                    }
                }
            }
    },
    methods:{
        initRequestData(){
            var ui = this.$userInfo;
            var token  =this.$token;

            requestData.username = ui.username;
            requestData.roleType = ui.roleType;
            requestData.tranType = tranTypeA;
            requestData.token = token;

            if(requestData.roleType&&requestData.roleType==='R_ENT'){
                console.log(ui);
                this.companyId = ui.roleName;
            }
            if(!ui.roleName) 
                delete requestData.roleName;
            else
                requestData.roleName = ui.roleName;

            if(!ui.place1) 
                delete requestData.place1;
            else
                requestData.place1 = ui.place1;
            
            if(!ui.place2) 
                delete requestData.place2;
            else
                requestData.place2 = ui.place2;
            

            var d = new Date();    
            var th = d.getHours();           
            if(th==0){
                var yesterday = (new Date()).setTime(d.getTime()-60*60*1000);
                var ym = yesterday.getMonth()+1;
                var yd = yesterday.getDate();
                if(ym>=1 && ym <= 9)
                    ym = '0' + ym;
                if(yd>=1 && yd<=9)
                    yd = '0'+yd;
                var yy = yesterday.getFullYear()+'-'+ym+'-'+'yd';
                requestData.timeRange = yy+' 00:00:00&'+yy+' 23:59:59';    
                
            }else{
                var tm = d.getMonth()+1;
                var td = d.getDate();
                th = th-1;
                if(tm>=1 && tm <= 9)
                    tm = '0' + tm;
                if(td>=1 && td<=9)
                    td = '0'+td;
                if(th>=0 && th<=9)
                    th = '0'+th;
                var t = d.getFullYear()+'-'+tm+'-'+td;
                requestData.timeRange = t+' 00:00:00&'+t+' '+th+':59:59';
                
            }
        },
        setData(res){
            var dataFulCa = [];
            var dataFulCo = [];

            var engTMuMap = {};
            var time = [];
            res.xs[0].forEach(function(e){
                var dateTime = e.split(" ",2);
                time.push(dateTime[1]);
            });
            res.engTypOther.forEach(function(eng) {
                eng.engTypTMu.forEach(function(TMu) {
                    if(!engTMuMap[eng.baseTyp]) 
                        engTMuMap[eng.baseTyp] = {};
                    if(!engTMuMap[eng.baseTyp][TMu.type]) 
                            engTMuMap[eng.baseTyp][TMu.type] = [0,0];
                    var t = engTMuMap[eng.baseTyp][TMu.type];
                    t[0] = (TMu.typDatOfAllEng / TMu.typDatOfAllLen*100).toFixed(2);
                    t[1] = (TMu.typDatOfAllCo / TMu.typDatOfAllLen*100).toFixed(2);
                    engTMuMap[eng.baseTyp][TMu.type] = t;              
                });
            });

            console.log(engTMuMap);
            var relTimeSerise = [];
            var relTimeCoSerise = [];
            res.xs[1].forEach(function(e){
                var t = engTMuMap[e];
                var tmpS = {
                    name: e,
                    type: 'bar',
                    data: []
                };
                var tmpSCo = {
                    name: e,
                    type:'line',
                    smooth:true,
                    symbol: 'none',
                    sampling: 'average',
                    data: []
                };

                if(!t){
                    // for(var i =0;i<res.xs[0].length;i++)
                    //     tmpS.data.push(0);
                }
                else{
                    res.xs[0].forEach(function(mu){
                        var t1 = t[mu];
                        if(!t1) {
                            tmpS.data.push(0);
                            tmpSCo.data.push(0);
                        }
                        else {
                            tmpS.data.push(t1[0]);
                            tmpSCo.data.push(t1[1]);
                        }
                    });
                }
                relTimeSerise.push(tmpS);
                relTimeCoSerise.push(tmpSCo);
            });
            relTimeSerise[1].yAxisIndex =1;
            relTimeData.splice(0,relTimeData.length);
            relTimeData.push(res.xs[1]);
            relTimeData.push(time);
            relTimeData.push(relTimeSerise);

            relTimeCoSerise[1].yAxisIndex=1;
            relTimeDataCo.splice(0,relTimeDataCo.length);
            relTimeDataCo.push(res.xs[1]);
            relTimeDataCo.push(time);
            relTimeDataCo.push(relTimeCoSerise);


        },

        selectDate(tr){
            //console.log('timeRange r ='+tr);
            if(!tr||tr==='')
                requestData['timeRange'] = '';
            else{
                var date = new Date();
                var year = date.getFullYear();
                var month = date.getMonth()+1;
                if(month>=1&&month<=9)
                    month = '0'+month;
                var day = date.getDate();
                if(day>=1&&day<=9)
                    day = '0'+day;
                var today = year + '-' + month + '-' + day;
                //console.log(today);
                if(today == tr){
                    var hour = date.getHours()-1;
                    if(hour>=0 && hour <=9)
                        hour = '0'+ hour;
                    requestData.timeRange = tr+' 00:00:00&'+tr+' '+hour+':59:59';
                }else{
                    requestData['timeRange']=tr+' 00:00:00&' +tr +' 23:59:59';
                }
                
            }
                   
        },
        
        getData(){

            if(!requestData.timeRange||requestData.timeRange===''){
                this.$message({
                    message: '日期不能为空，请选择日期',
                    type: 'warning',
                    showClose:true,
                    duration:2500
                });
                return ;
            }

            if(this.inputCode==''){
                this.$confirm('未输入监测项（城市/企业/车辆）名称，默认查询浙江省全部地区的实时能耗数据，是否继续？','提示', {
                    confirmButtonText: '确定',
                    cancelButtonText: '取消',
                    type: 'warning'
                }).then(() => {
                    delete requestData.carId;
                    delete requestData.shipId;
                    delete requestData.companyId;
                    delete requestData.cityType;
                    this.getDataFromService(requestData);
                }).catch(() => {
                    return;         
                });
            }else{
                if(this.inputRank==''){
                    this.$message({
                        message: '监测项不能为空，请选择监测项',
                        type: 'warning',
                        showClose:true,
                        duration:2500
                    });
                    return ;
                }else{
                    if(this.inputRank=='地市'){
                        delete requestData.carId;
                        delete requestData.shipId;
                        delete requestData.companyId;
                        requestData.cityType = this.inputCode;
                    }
                    if(this.inputRank=='企业'){
                        delete requestData.carId;
                        delete requestData.shipId;
                        delete requestData.cityType;
                        requestData.companyId = this.inputCode;
                    }
                    if(this.inputRank=='车辆'){
                        delete requestData.cityType;
                        delete requestData.cityType;
                        requestData.carId = this.inputCode;
                        requestData.shipId = this.inputCode;
                    }
                    this.getDataFromService(requestData);
                }
                
            }
            
        },

        selectInputRank(ir){
           

        },
        getDataFromService(requestData){
            console.log(requestData);
            var _this = this;
            relTimeChart.showLoading({text:'加载中'});
            relTimeChartCo.showLoading({text:'加载中'});
            $.get(this.Constant.ajaxAddress+this.Constant.relTimeAjax,requestData).
            done(function (res){
                relTimeChart.hideLoading();
                relTimeChartCo.hideLoading();
                if(res.errCode==30){//data ok
                    _this.setData(res);

                    var c = '浙江省';
                    if(_this.inputRank=='地市'&&_this.inputCode!="")
                        c = res.cityType+"市";
                    if(_this.inputRank=='车辆'&&_this.inputCode!="")
                        c = '车辆:'+res.carId;
                    if(_this.inputRank=='企业'&&_this.inputCode!="")
                        c = '企业:' + res.companyId;
                    
                    option.title.text = c +' '+ res.timeRange.substring(0,10)+' '+res.tranType+'实时油电百公里消耗';
                    option.legend.data = relTimeData[0];
                    option.xAxis.data = relTimeData[1];
                    option.series = relTimeData[2];
                    relTimeChart.clear();
                    relTimeChart.setOption(option);

                    optionCo.title.text = c +' '+ res.timeRange.substring(0,10)+' '+res.tranType+' 实时标煤百公里消耗';
                    optionCo.legend.data = relTimeDataCo[0];
                    optionCo.xAxis.data = relTimeDataCo[1];
                    optionCo.series = relTimeDataCo[2];
                    relTimeChartCo.clear();
                    relTimeChartCo.setOption(optionCo);
                }else if(res.errCode==31){ // data err
                    window.log('unknow err');
                }else if(res.errCode==44){ // auth 
                    _this.$router.push('/login');
                }

            });
        }
    },
    mounted:function(){
        relTimeChart =  echarts.init(document.getElementById('relTimeChart'));
        relTimeChartCo = echarts.init(document.getElementById('relTimeChartCo'));
        relTimeChart.setOption(option);
        relTimeChartCo.setOption(optionCo);
        this.initRequestData();


        
        //this.timeRange =  t;
        //this.getDataFromService(requestData);
    },
    updated:function(){

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
                margin-left: 30px;
                position: relative;
            }
            .chart-content{
                overflow: hidden;
            }
        }
        
    }
    .el-row {
        margin-bottom: 15px;
        &:last-child {
        margin-bottom: 0;
        }
    }
    .el-select {
        width: 130px;
    }
    .el-col {
        padding: 20px 20px;
    }
</style>


