<template>
    <section class="chart">
        <el-row>
            <el-col class="chart-container">
                <!--<div class="chart-header">
                    <el-date-picker
                        v-model="timeRange"
                        type="daterange"
                        placeholder="选择日期范围"
                        range-separator = ':'
                        @change="selectOther">
                    </el-date-picker>
                </div>-->
                
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
                <div class="chart-header">
                    <el-select v-model="tranType" filterable placeholder="请选择交通工具" @change="trafficSelectChange">
                        <el-option key="道路客运" label="道路客运" value="道路客运"></el-option>
                        <el-option key="道路货运" label="道路货运" value="道路货运"></el-option>
                        <el-option key="公交客运" label="公交客运" value="公交客运"></el-option>
                        <el-option key="出租客运" label="出租车运输" value="出租客运"></el-option>
                        <el-option key="内河运输" label="内河运输" value="内河运输"></el-option>
                        <el-option key="海洋货运" label="海洋货运" value="海洋货运"></el-option>
                        <el-option key="海洋客运" label="海洋客运" value="海洋客运"></el-option>
                    </el-select>
                </div>
            
                <div class="chart-header2">
                    统计期：{{ countDate }}
                </div>
                
            </el-col>
        </el-row>
        <el-row> 
            <el-col class="chart-container">
                <div id="cityTypeEnergyPie" style="width:100%; height:400px;"></div>
            </el-col>
        </el-row>

    </section>
</template>

<script>
    import {getCookie,delCookie,setCookie} from '../../common/js/Cookie.js';
    var dataForCityEngAll = [];
    var cityTypeEnergyPie;

    var beforTimeRange = '';
    var beforTran ='';

    var requestData = {}; 

    var optionPi={
        title:{
            text: '各地市能耗构成图',
            subtext:'单位：万吨标准煤',
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
            right:'3%'
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

    function setData(res){
        
        var engerData = {};
        var eng_all_for_PI = []; //饼图填充数据

        res.citTypOther.forEach(function(element){
            var t = engerData[element.baseTyp];
            if(!t) t=[0];
            t[0] += element.baseTypDatOfAllEng;
            //t[1] += element.baseTypDatOfAllLen;
            engerData[element.baseTyp] = t;
        });
        res.xs[0].forEach(function(e1){
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

        dataForCityEngAll.splice(0,dataForCityEngAll.length);
        dataForCityEngAll.push(res.xs[0]);
        dataForCityEngAll.push(eng_all_for_PI);

    }


    import echarts from 'echarts'
    export default{
        data(){
            return{
                timeRange:'',
                tranType:'道路客运',
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
                },
            }
        },
        methods:{
            initRequestData(requestData){
                var date = new Date;
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
                requestData.tranType = "道路客运";

                this.countDate = year+'年'+month+'月';
            },
            getDataFromService(requestData){
               var _this = this;
               cityTypeEnergyPie.showLoading({text:'加载中'});
                $.get(this.Constant.ajaxAddress+this.Constant.citenergyAjax,requestData).
                done(function (res){
                        cityTypeEnergyPie.hideLoading();
                        if(res.errCode==30){//data ok
                        setData(res);
                        optionPi.legend.data = dataForCityEngAll[0];
                        optionPi.series[0].data = dataForCityEngAll[1];
                        cityTypeEnergyPie.clear();
                        cityTypeEnergyPie.setOption(optionPi);
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
                    if(this.tranType==''||!this.tranType)
                        return;
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
            trafficSelectChange(tt){
                console.log(tt+'   before=' + beforTran);
                if(!tt||tt=='')
                    return ;
                if(this.beginDate==''||this.endDate=='')
                    return;
                requestData['tranType']=tt;
                this.getDataFromService(requestData);
                beforTran = tt;              
            }
        },
        mounted: function () {
            cityTypeEnergyPie = echarts.init(document.getElementById('cityTypeEnergyPie'));
            cityTypeEnergyPie.setOption(optionPi);
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
                //margin-bottom: 20px;
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