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
                <div class="chart-header">
                    <el-select v-model="cityType" filterable placeholder="请选择城市" @change="citySelectChange">
                        <el-option
                            v-for="item in optionCity"
                            :key="item.value"
                            :label="item.label"
                            :value="item.value"
                            :disabled="item.disabled">
                        </el-option>
                    </el-select>
                </div>
                <div class="chart-header2">
                    统计期：{{ countDate }}
                </div>
            </el-col>
        </el-row>

        <el-row> 
            <el-col class="chart-container">
                <div id="trafficTypeEnergyPie" style="width:100%; height:400px;"></div>
            </el-col>
        </el-row>
    </section>
</template>

<script>
    import {getCookie,delCookie,setCookie} from '../../common/js/Cookie.js';
    var dataForTranEngAll = [];
    var trafficTypeEnergyPie;

    

    var requestData = {};

    var optionPi={
        title:{
            //text: '各运输类型能耗构成图',
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

        res.traTypOther.forEach(function(element){
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

        dataForTranEngAll.splice(0,dataForTranEngAll.length);
        dataForTranEngAll.push(res.xs[0]);
        dataForTranEngAll.push(eng_all_for_PI);
    }

    import echarts from 'echarts'
    export default{
        data(){
            return{
                cityType:'',
                optionCity:[],
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
                var year = date.getFullYear().toString();
                var token = getCookie('token');
                var month = date.getMonth();
                if(month>=1 && month<=9)
                    month = '0'+month;
                var userInfo = JSON.parse(getCookie('userInfo'));
                requestData.token = token;
                requestData.username = userInfo.name;
                if(userInfo.roleName!=null && userInfo.roleName!="")
                    requestData.roleName = userInfo.roleName;
                requestData.roleType = userInfo.roleType;
                if(userInfo.place1!=null && userInfo.place1!=""){
                    requestData.place1 =userInfo.place1;
                    requestData.cityType = userInfo.place1;
                }else{
                    requestData.cityType = "杭州";
                }
                if(userInfo.place2!=null && userInfo.place2!="")
                    requestData.place2 = userInfo.place2;          
                requestData.timeRange = year+'-'+month+'-01:'+year+'-'+month+'-31';

                this.countDate = year+'年'+month+'月';
            },
            initSelectBox(){
                var userInfo = JSON.parse(getCookie('userInfo'));
                var city = ['杭州','宁波','温州','金华','湖州','绍兴','嘉兴','台州','舟山','衢州','丽水'];
                var isDisabled;
                this.optionCity= city.map(item => {
                    isDisabled = false
                    if(userInfo.place1!=null && userInfo.place1!="" && userInfo.place1!=item)
                        isDisabled = true;
                    return { value: item, label: item ,disabled: isDisabled};
                });
            },
            getDataFromService(requestData){
                var _this = this;
                trafficTypeEnergyPie.showLoading({text:'加载中'});
                $.get(this.Constant.ajaxAddress+this.Constant.trafficenergyAjax,requestData).
                done(function (res){
                    trafficTypeEnergyPie.hideLoading();
                    if(res.errCode==30){//data ok
                        if(requestData.place2)
                            var _title = requestData.cityType+'市'+requestData.place2+' 交通方式能耗构成图'
                        else
                            var _title = requestData.cityType+'市'+' 交通方式能耗构成图'
                        optionPi.title.text = _title;
                        setData(res);
                        //console.log(res); //
                        optionPi.legend.data = dataForTranEngAll[0];
                        optionPi.series[0].data = dataForTranEngAll[1];
                        trafficTypeEnergyPie.clear();
                        trafficTypeEnergyPie.setOption(optionPi);
                    }else if(res.errCode==31){ // data err
                        window.log('unknow err');
                    }else if(res.errCode==44){ // auth 
                        _this.$router.push('/login');
                    }

                });
                
            },
            selectOther(){  
                if(this.beginDate!='' && this.endDate!='' ){
                    if(this.beginDate > this.endDate){
                        this.$message({
                            showClose: true,
                            message: '起始年月不能大于结束年月',
                            type: 'warning',
                            duration:2500
                        });
                        return;
                    }
                    if(this.cityType==''||!this.cityType)
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
            citySelectChange(ct){
                //console.log(ct+'   before=' + beforCity);
                if(!ct||ct=='')
                    return ;
                if(this.beginDate==''||this.endDate=='')
                    return;
                requestData['cityType']=ct;
                this.getDataFromService(requestData);
            }

        },
        mounted: function () {
            trafficTypeEnergyPie = echarts.init(document.getElementById('trafficTypeEnergyPie'));
            trafficTypeEnergyPie.setOption(optionPi);
            this.initSelectBox();
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