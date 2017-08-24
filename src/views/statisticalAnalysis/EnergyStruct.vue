<template>
    <section class="chart">
        <el-row> 
            <!-- <el-col style = "align:center;" class="chart-container">  
                <div><h4>城市能耗图</h4></div>
            </el-col> -->
            <el-col class="chart-container">
                <h3 align="center">城市能耗图</h3>
                <div class="chart-header">
                    <el-date-picker
                        v-model="dateSelectValue"
                        type="daterange"
                        placeholder="选择日期范围"
                        :picker-options="pickerOptions2">
                    </el-date-picker>
                </div>
            </el-col>
        </el-row>
        <el-row >
            <el-col :xs="24" :sm="24" :md="8" :lg="8" class="chart-container">  
                <div id="city1" style="width:100%; height:300px;" class="chart-content"></div>
            </el-col>
            <el-col :xs="24" :sm="24" :md="8" :lg="8" class="chart-container">  
                <div id="city2" style="width:100%; height:300px;" class="chart-content"></div>
            </el-col>
            <el-col :xs="24" :sm="24" :md="8" :lg="8" class="chart-container">  
                <div id="city3" style="width:100%; height:300px;" class="chart-content"></div>
            </el-col>
        </el-row>
        <el-row>
            <el-col :xs="24" :sm="24" :md="8" :lg="8" class="chart-container">  
                <div id="city4" style="width:100%; height:300px;" class="chart-content"></div>
            </el-col>
            <el-col :xs="24" :sm="24" :md="8" :lg="8" class="chart-container">  
                <div id="city5" style="width:100%; height:300px;" class="chart-content"></div>
            </el-col>
            <el-col :xs="24" :sm="24" :md="8" :lg="8" class="chart-container">  
                <div id="city6" style="width:100%; height:300px;" class="chart-content"></div>
            </el-col>
        </el-row>
        <el-row>
            <el-col :xs="24" :sm="24" :md="8" :lg="8" class="chart-container">  
                <div id="city7" style="width:100%; height:300px;" class="chart-content"></div>
            </el-col>
            <el-col :xs="24" :sm="24" :md="8" :lg="8" class="chart-container">  
                <div id="city8" style="width:100%; height:300px;" class="chart-content"></div>
            </el-col>
            <el-col :xs="24" :sm="24" :md="8" :lg="8" class="chart-container">  
                <div id="city9" style="width:100%; height:300px;" class="chart-content"></div>
            </el-col>
        </el-row>
        <el-row>
            <el-col :xs="24" :sm="24" :md="8" :lg="8" class="chart-container">  
                <div id="city10" style="width:100%; height:300px;" class="chart-content"></div>
            </el-col>
            <el-col :xs="24" :sm="24" :md="8" :lg="8" class="chart-container">  
                <div id="city11" style="width:100%; height:300px;" class="chart-content"></div>
            </el-col>
             <el-col :xs="24" :sm="24" :md="8" :lg="8" class="chart-container">  
                <div  style="width:100%; height:300px;" class="chart-content"></div>
            </el-col>
        </el-row>
        <el-row> 
            <el-col class="chart-container">
                <h3 align="center">行业能耗结构图</h3>
                <div class="chart-header">
                    <el-date-picker
                        v-model="dateSelectValue"
                        type="daterange"
                        placeholder="选择日期范围"
                        :picker-options="pickerOptions2">
                    </el-date-picker>
                </div>
            </el-col>
        </el-row>
        <el-row>
            <el-col :xs="24" :sm="24" :md="8" :lg="8" class="chart-container">  
                <div id="industry1" style="width:100%; height:300px;" class="chart-content"></div>
            </el-col>
            <el-col :xs="24" :sm="24" :md="8" :lg="8" class="chart-container">  
                <div id="industry2" style="width:100%; height:300px;" class="chart-content"></div>
            </el-col>
            <el-col :xs="24" :sm="24" :md="8" :lg="8" class="chart-container">  
                <div id="industry3" style="width:100%; height:300px;" class="chart-content"></div>
            </el-col>
        </el-row>
        <el-row> 
            <el-col class="chart-container">
                <h3 align="center">运输工具能耗结构图</h3>
                <div class="chart-header">
                    <el-date-picker
                        v-model="dateSelectValue"
                        type="daterange"
                        placeholder="选择日期范围"
                        :picker-options="pickerOptions2">
                    </el-date-picker>
                </div>
            </el-col>
        </el-row>
        <el-row>
            <el-col :xs="24" :sm="24" :md="8" :lg="8" class="chart-container">  
                <div id="transport1" style="width:100%; height:300px;" class="chart-content"></div>
            </el-col>
            <el-col :xs="24" :sm="24" :md="8" :lg="8" class="chart-container">  
                <div id="transport2" style="width:100%; height:300px;" class="chart-content"></div>
            </el-col>
            <el-col :xs="24" :sm="24" :md="8" :lg="8" class="chart-container">  
                <div id="transport3" style="width:100%; height:300px;" class="chart-content"></div>
            </el-col>
        </el-row>
    </section>
</template>    

<script>
    import echarts from 'echarts'

    var currentCityId = 0;
    export default {
        data(){
            return{
                dateSelectValue:'',
                pickerOptions2: {
                    shortcuts: [{
                        text: '最近一周',
                        onClick(picker) {
                            const end = new Date();
                            const start = new Date();
                            start.setTime(start.getTime() - 3600 * 1000 * 24 * 7);
                            picker.$emit('pick', [start, end]);
                        }
                    }, {
                        text: '最近一个月',
                        onClick(picker) {
                            const end = new Date();
                            const start = new Date();
                            start.setTime(start.getTime() - 3600 * 1000 * 24 * 30);
                            picker.$emit('pick', [start, end]);
                        }
                    }, {
                            text: '最近三个月',
                            onClick(picker) {
                            const end = new Date();
                            const start = new Date();
                            start.setTime(start.getTime() - 3600 * 1000 * 24 * 90);
                            picker.$emit('pick', [start, end]);
                        }
                    }]
                }
            }
        },
        methods:{
            drawcity1(){
                let city1 = echarts.init(document.getElementById('city1'));

                let option={
                    title:{
                        text: '杭州市能耗结构图',
                        x: 'center'
                    },
                    tooltip : {
                        trigger: 'item',
                        formatter: "{a} <br/>{b} : {c} ({d}%)"
                    },
                    legend: {
                        orient: 'vertical',
                        left: 'left',
                        y:'bottom',
                        data: ['电耗','油耗','气耗']
                    },
                    toolbox: {
                        show : true,
                        feature : {
                            mark : {show: true},                 
                            dataView : {show: true, readOnly: false},
                            saveAsImage : {show: true},
                        }
                    },
                    series : [
                        {
                            type: 'pie',
                            radius : '80%',
                            center: ['50%', '50%'],
                            data:[],
                            roseType: 'area',
                            calculable : true,
                            label:{
                                normal:{
                                    testStyle:{
                                        color: 'rgba(255, 255, 255, 0.5)'
                                    }
                                }
                            },
                            labelLine: {
                                 normal: {
                                    smooth: 0.2,
                                    length: 10,
                                    length2: 20
                                }
                            
                            },
                            data:[
                                {value:242,name:'电耗'},
                                {value:339,name:'油耗'},
                                {value:113,name:'气耗'},
                            ]
                            
                        }
                    ]
                };
                city1.setOption(option);
            },
            drawcity2(){
                let city2 = echarts.init(document.getElementById('city2'));
                let option={
                    title:{
                        text: '宁波市能耗结构图',
                        x: 'center'
                    },
                    tooltip : {
                        trigger: 'item',
                        formatter: "{a} <br/>{b} : {c} ({d}%)"
                    },
                    legend: {
                        orient: 'vertical',
                        left: 'left',
                        y:'bottom',
                        data: ['电耗','油耗','气耗']
                    },
                    toolbox: {
                        show : true,
                        feature : {
                            mark : {show: true},                 
                            dataView : {show: true, readOnly: false},
                            saveAsImage : {show: true},
                        }
                    },
                    series : [
                        {
                            type: 'pie',
                            radius : '80%',
                            center: ['50%', '50%'],
                            data:[],
                            roseType: 'area',
                            calculable : true,
                            label:{
                                normal:{
                                    testStyle:{
                                        color: 'rgba(255, 255, 255, 0.5)'
                                    }
                                }
                            },
                            labelLine: {
                                 normal: {
                                    smooth: 0.2,
                                    length: 10,
                                    length2: 20
                                }
                            
                            },
                            data:[
                                {value:218,name:'电耗'},
                                {value:250,name:'油耗'},
                                {value:409,name:'气耗'},
                            ]
                            
                        }
                    ]
                };
                city2.setOption(option);
            },
            drawcity3(){
                let city3 = echarts.init(document.getElementById('city3'));
                let option={
                    title:{
                        text: '温州市能耗结构图',
                        x: 'center'
                    },
                    tooltip : {
                        trigger: 'item',
                        formatter: "{a} <br/>{b} : {c} ({d}%)"
                    },
                    legend: {
                        orient: 'vertical',
                        left: 'left',
                        y:'bottom',
                        data: ['电耗','油耗','气耗']
                    },
                    toolbox: {
                        show : true,
                        feature : {
                            mark : {show: true},                 
                            dataView : {show: true, readOnly: false},
                            saveAsImage : {show: true},
                        }
                    },
                    series : [
                        {
                            type: 'pie',
                            radius : '80%',
                            center: ['50%', '50%'],
                            data:[],
                            roseType: 'area',
                            calculable : true,
                            label:{
                                normal:{
                                    testStyle:{
                                        color: 'rgba(255, 255, 255, 0.5)'
                                    }
                                }
                            },
                            labelLine: {
                                 normal: {
                                    smooth: 0.2,
                                    length: 10,
                                    length2: 20
                                }
                            
                            },
                            data:[
                                {value:331,name:'电耗'},
                                {value:222,name:'油耗'},
                                {value:113,name:'气耗'},
                            ]
                            
                        }
                    ]
                };
                city3.setOption(option);
            },
            drawcity4(){
                let city4 = echarts.init(document.getElementById('city4'));

                let option={
                    title:{
                        text: '嘉兴市能耗结构图',
                        x: 'center'
                    },
                    tooltip : {
                        trigger: 'item',
                        formatter: "{a} <br/>{b} : {c} ({d}%)"
                    },
                    legend: {
                        orient: 'vertical',
                        left: 'left',
                        y:'bottom',
                        data: ['电耗','油耗','气耗']
                    },
                    toolbox: {
                        show : true,
                        feature : {
                            mark : {show: true},                 
                            dataView : {show: true, readOnly: false},
                            saveAsImage : {show: true},
                        }
                    },
                    series : [
                        {
                            type: 'pie',
                            radius : '80%',
                            center: ['50%', '50%'],
                            data:[],
                            roseType: 'area',
                            calculable : true,
                            label:{
                                normal:{
                                    testStyle:{
                                        color: 'rgba(255, 255, 255, 0.5)'
                                    }
                                }
                            },
                            labelLine: {
                                 normal: {
                                    smooth: 0.2,
                                    length: 10,
                                    length2: 20
                                }
                            
                            },
                            data:[
                                {value:132,name:'电耗'},
                                {value:339,name:'油耗'},
                                {value:222,name:'气耗'},
                            ]
                            
                        }
                    ]
                };
                city4.setOption(option);
            },
            drawcity5(){
                let city5 = echarts.init(document.getElementById('city5'));

                let option={
                    title:{
                        text: '湖州市能耗结构图',
                        x: 'center'
                    },
                    tooltip : {
                        trigger: 'item',
                        formatter: "{a} <br/>{b} : {c} ({d}%)"
                    },
                    legend: {
                        orient: 'vertical',
                        left: 'left',
                        y:'bottom',
                        data: ['电耗','油耗','气耗']
                    },
                    toolbox: {
                        show : true,
                        feature : {
                            mark : {show: true},                 
                            dataView : {show: true, readOnly: false},
                            saveAsImage : {show: true},
                        }
                    },
                    series : [
                        {
                            type: 'pie',
                            radius : '80%',
                            center: ['50%', '50%'],
                            data:[],
                            roseType: 'area',
                            calculable : true,
                            label:{
                                normal:{
                                    testStyle:{
                                        color: 'rgba(255, 255, 255, 0.5)'
                                    }
                                }
                            },
                            labelLine: {
                                 normal: {
                                    smooth: 0.2,
                                    length: 10,
                                    length2: 20
                                }
                            
                            },
                            data:[
                                {value:142,name:'电耗'},
                                {value:330,name:'油耗'},
                                {value:213,name:'气耗'},
                            ]
                            
                        }
                    ]
                };
                city5.setOption(option);
            },
            drawcity6(){
                let city6 = echarts.init(document.getElementById('city6'));

                let option={
                    title:{
                        text: '绍兴市能耗结构图',
                        x: 'center'
                    },
                    tooltip : {
                        trigger: 'item',
                        formatter: "{a} <br/>{b} : {c} ({d}%)"
                    },
                    legend: {
                        orient: 'vertical',
                        left: 'left',
                        y:'bottom',
                        data: ['电耗','油耗','气耗']
                    },
                    toolbox: {
                        show : true,
                        feature : {
                            mark : {show: true},                 
                            dataView : {show: true, readOnly: false},
                            saveAsImage : {show: true},
                        }
                    },
                    series : [
                        {
                            type: 'pie',
                            radius : '80%',
                            center: ['50%', '50%'],
                            data:[],
                            roseType: 'area',
                            calculable : true,
                            label:{
                                normal:{
                                    testStyle:{
                                        color: 'rgba(255, 255, 255, 0.5)'
                                    }
                                }
                            },
                            labelLine: {
                                 normal: {
                                    smooth: 0.2,
                                    length: 10,
                                    length2: 20
                                }
                            
                            },
                            data:[
                                {value:122,name:'电耗'},
                                {value:139,name:'油耗'},
                                {value:313,name:'气耗'},
                            ]
                            
                        }
                    ]
                };
                city6.setOption(option);
            },
            drawcity7(){
                let city7 = echarts.init(document.getElementById('city7'));

                let option={
                    title:{
                        text: '衢州市能耗结构图',
                        x: 'center'
                    },
                    tooltip : {
                        trigger: 'item',
                        formatter: "{a} <br/>{b} : {c} ({d}%)"
                    },
                    legend: {
                        orient: 'vertical',
                        left: 'left',
                        y:'bottom',
                        data: ['电耗','油耗','气耗']
                    },
                    toolbox: {
                        show : true,
                        feature : {
                            mark : {show: true},                 
                            dataView : {show: true, readOnly: false},
                            saveAsImage : {show: true},
                        }
                    },
                    series : [
                        {
                            type: 'pie',
                            radius : '80%',
                            center: ['50%', '50%'],
                            data:[],
                            roseType: 'area',
                            calculable : true,
                            label:{
                                normal:{
                                    testStyle:{
                                        color: 'rgba(255, 255, 255, 0.5)'
                                    }
                                }
                            },
                            labelLine: {
                                 normal: {
                                    smooth: 0.2,
                                    length: 10,
                                    length2: 20
                                }
                            
                            },
                            data:[
                                {value:122,name:'电耗'},
                                {value:339,name:'油耗'},
                                {value:333,name:'气耗'},
                            ]
                            
                        }
                    ]
                };
                city7.setOption(option);
            },
            drawcity8(){
                let city8 = echarts.init(document.getElementById('city8'));
                let option={
                    title:{
                        text: '金华市能耗结构图',
                        x: 'center'
                    },
                    tooltip : {
                        trigger: 'item',
                        formatter: "{a} <br/>{b} : {c} ({d}%)"
                    },
                    legend: {
                        orient: 'vertical',
                        left: 'left',
                        y:'bottom',
                        data: ['电耗','油耗','气耗']
                    },
                    toolbox: {
                        show : true,
                        feature : {
                            mark : {show: true},                 
                            dataView : {show: true, readOnly: false},
                            saveAsImage : {show: true},
                        }
                    },
                    series : [
                        {
                            type: 'pie',
                            radius : '80%',
                            center: ['50%', '50%'],
                            data:[],
                            roseType: 'area',
                            calculable : true,
                            label:{
                                normal:{
                                    testStyle:{
                                        color: 'rgba(255, 255, 255, 0.5)'
                                    }
                                }
                            },
                            labelLine: {
                                 normal: {
                                    smooth: 0.2,
                                    length: 10,
                                    length2: 20
                                }
                            
                            },
                            data:[
                                {value:218,name:'电耗'},
                                {value:230,name:'油耗'},
                                {value:209,name:'气耗'},
                            ]
                            
                        }
                    ]
                };
                city8.setOption(option);
            },
            drawcity9(){
                let city9 = echarts.init(document.getElementById('city9'));
                let option={
                    title:{
                        text: '舟山市能耗结构图',
                        x: 'center'
                    },
                    tooltip : {
                        trigger: 'item',
                        formatter: "{a} <br/>{b} : {c} ({d}%)"
                    },
                    legend: {
                        orient: 'vertical',
                        left: 'left',
                        y:'bottom',
                        data: ['电耗','油耗','气耗']
                    },
                    toolbox: {
                        show : true,
                        feature : {
                            mark : {show: true},                 
                            dataView : {show: true, readOnly: false},
                            saveAsImage : {show: true},
                        }
                    },
                    series : [
                        {
                            type: 'pie',
                            radius : '80%',
                            center: ['50%', '50%'],
                            data:[],
                            roseType: 'area',
                            calculable : true,
                            label:{
                                normal:{
                                    testStyle:{
                                        color: 'rgba(255, 255, 255, 0.5)'
                                    }
                                }
                            },
                            labelLine: {
                                 normal: {
                                    smooth: 0.2,
                                    length: 10,
                                    length2: 20
                                }
                            
                            },
                            data:[
                                {value:111,name:'电耗'},
                                {value:230,name:'油耗'},
                                {value:538,name:'气耗'},
                            ]
                            
                        }
                    ]
                };
                city9.setOption(option);
            },
            drawcity10(){
                let city10 = echarts.init(document.getElementById('city10'));
                let option={
                    title:{
                        text: '台州市能耗结构图',
                        x: 'center'
                    },
                    tooltip : {
                        trigger: 'item',
                        formatter: "{a} <br/>{b} : {c} ({d}%)"
                    },
                    legend: {
                        orient: 'vertical',
                        left: 'left',
                        y:'bottom',
                        data: ['电耗','油耗','气耗']
                    },
                    toolbox: {
                        show : true,
                        feature : {
                            mark : {show: true},                 
                            dataView : {show: true, readOnly: false},
                            saveAsImage : {show: true},
                        }
                    },
                    series : [
                        {
                            type: 'pie',
                            radius : '80%',
                            center: ['50%', '50%'],
                            data:[],
                            roseType: 'area',
                            calculable : true,
                            label:{
                                normal:{
                                    testStyle:{
                                        color: 'rgba(255, 255, 255, 0.5)'
                                    }
                                }
                            },
                            labelLine: {
                                 normal: {
                                    smooth: 0.2,
                                    length: 10,
                                    length2: 20
                                }
                            
                            },
                            data:[
                                {value:111,name:'电耗'},
                                {value:450,name:'油耗'},
                                {value:138,name:'气耗'},
                            ]
                            
                        }
                    ]
                };
                city10.setOption(option);
            },
            drawcity11(){
                let city11 = echarts.init(document.getElementById('city11'));
                let option={
                    title:{
                        text: '丽水市能耗结构图',
                        x: 'center'
                    },
                    tooltip : {
                        trigger: 'item',
                        formatter: "{a} <br/>{b} : {c} ({d}%)"
                    },
                    legend: {
                        orient: 'vertical',
                        left: 'left',
                        y:'bottom',
                        data: ['电耗','油耗','气耗']
                    },
                    toolbox: {
                        show : true,
                        feature : {
                            mark : {show: true},                 
                            dataView : {show: true, readOnly: false},
                            saveAsImage : {show: true},
                        }
                    },
                    series : [
                        {
                            type: 'pie',
                            radius : '80%',
                            center: ['50%', '50%'],
                            data:[],
                            roseType: 'area',
                            calculable : true,
                            label:{
                                normal:{
                                    testStyle:{
                                        color: 'rgba(255, 255, 255, 0.5)'
                                    }
                                }
                            },
                            labelLine: {
                                 normal: {
                                    smooth: 0.2,
                                    length: 10,
                                    length2: 20
                                }
                            
                            },
                            data:[
                                {value:111,name:'电耗'},
                                {value:150,name:'油耗'},
                                {value:338,name:'气耗'},
                            ]
                            
                        }
                    ]
                };
                city11.setOption(option);
            },
            drawindustry1(){
                let industry1 = echarts.init(document.getElementById('industry1'));
                let option={
                    title:{
                        text: '运输业能耗结构图',
                        x: 'center'
                    },
                    tooltip : {
                        trigger: 'item',
                        formatter: "{a} <br/>{b} : {c} ({d}%)"
                    },
                    legend: {
                        orient: 'vertical',
                        left: 'left',
                        y:'bottom',
                        data: ['电耗','油耗','气耗']
                    },
                    toolbox: {
                        show : true,
                        feature : {
                            mark : {show: true},                 
                            dataView : {show: true, readOnly: false},
                            saveAsImage : {show: true},
                        }
                    },
                    series : [
                        {
                            type: 'pie',
                            radius : '80%',
                            center: ['50%', '50%'],
                            data:[],
                            roseType: 'area',
                            calculable : true,
                            label:{
                                normal:{
                                    testStyle:{
                                        color: 'rgba(255, 255, 255, 0.5)'
                                    }
                                }
                            },
                            labelLine: {
                                 normal: {
                                    smooth: 0.2,
                                    length: 10,
                                    length2: 20
                                }
                            
                            },
                            data:[
                                {value:111,name:'电耗'},
                                {value:350,name:'油耗'},
                                {value:138,name:'气耗'},
                            ]
                            
                        }
                    ]
                };
                industry1.setOption(option);
            },
            drawindustry2(){
                let industry2 = echarts.init(document.getElementById('industry2'));
                let option={
                    title:{
                        text: '餐饮业能耗结构图',
                        x: 'center'
                    },
                    tooltip : {
                        trigger: 'item',
                        formatter: "{a} <br/>{b} : {c} ({d}%)"
                    },
                    legend: {
                        orient: 'vertical',
                        left: 'left',
                        y:'bottom',
                        data: ['电耗','油耗','气耗']
                    },
                    toolbox: {
                        show : true,
                        feature : {
                            mark : {show: true},                 
                            dataView : {show: true, readOnly: false},
                            saveAsImage : {show: true},
                        }
                    },
                    series : [
                        {
                            type: 'pie',
                            radius : '80%',
                            center: ['50%', '50%'],
                            data:[],
                            roseType: 'area',
                            calculable : true,
                            label:{
                                normal:{
                                    testStyle:{
                                        color: 'rgba(255, 255, 255, 0.5)'
                                    }
                                }
                            },
                            labelLine: {
                                 normal: {
                                    smooth: 0.2,
                                    length: 10,
                                    length2: 20
                                }
                            
                            },
                            data:[
                                {value:211,name:'电耗'},
                                {value:350,name:'油耗'},
                                {value:138,name:'气耗'},
                            ]
                            
                        }
                    ]
                };
                industry2.setOption(option);
            },
            drawindustry3(){
                let industry3 = echarts.init(document.getElementById('industry3'));
                let option={
                    title:{
                        text: '建筑业能耗结构图',
                        x: 'center'
                    },
                    tooltip : {
                        trigger: 'item',
                        formatter: "{a} <br/>{b} : {c} ({d}%)"
                    },
                    legend: {
                        orient: 'vertical',
                        left: 'left',
                        y:'bottom',
                        data: ['电耗','油耗','气耗']
                    },
                    toolbox: {
                        show : true,
                        feature : {
                            mark : {show: true},                 
                            dataView : {show: true, readOnly: false},
                            saveAsImage : {show: true},
                        }
                    },
                    series : [
                        {
                            type: 'pie',
                            radius : '80%',
                            center: ['50%', '50%'],
                            data:[],
                            roseType: 'area',
                            calculable : true,
                            label:{
                                normal:{
                                    testStyle:{
                                        color: 'rgba(255, 255, 255, 0.5)'
                                    }
                                }
                            },
                            labelLine: {
                                 normal: {
                                    smooth: 0.2,
                                    length: 10,
                                    length2: 20
                                }
                            
                            },
                            data:[
                                {value:111,name:'电耗'},
                                {value:150,name:'油耗'},
                                {value:338,name:'气耗'},
                            ]
                            
                        }
                    ]
                };
                industry3.setOption(option);
            },
            drawtransport1(){
                let transport1 = echarts.init(document.getElementById('transport1'));
                let option={
                    title:{
                        text: '公交车能耗结构图',
                        x: 'center'
                    },
                    tooltip : {
                        trigger: 'item',
                        formatter: "{a} <br/>{b} : {c} ({d}%)"
                    },
                    legend: {
                        orient: 'vertical',
                        left: 'left',
                        y:'bottom',
                        data: ['电耗','油耗','气耗']
                    },
                    toolbox: {
                        show : true,
                        feature : {
                            mark : {show: true},                 
                            dataView : {show: true, readOnly: false},
                            saveAsImage : {show: true},
                        }
                    },
                    series : [
                        {
                            type: 'pie',
                            radius : '80%',
                            center: ['50%', '50%'],
                            data:[],
                            roseType: 'area',
                            calculable : true,
                            label:{
                                normal:{
                                    testStyle:{
                                        color: 'rgba(255, 255, 255, 0.5)'
                                    }
                                }
                            },
                            labelLine: {
                                 normal: {
                                    smooth: 0.2,
                                    length: 10,
                                    length2: 20
                                }
                            
                            },
                            data:[
                                {value:311,name:'电耗'},
                                {value:150,name:'油耗'},
                                {value:138,name:'气耗'},
                            ]
                            
                        }
                    ]
                };
                transport1.setOption(option);
            },
            drawtransport2(){
                let transport2 = echarts.init(document.getElementById('transport2'));
                let option={
                    title:{
                        text: '大型货车能耗结构图',
                        x: 'center'
                    },
                    tooltip : {
                        trigger: 'item',
                        formatter: "{a} <br/>{b} : {c} ({d}%)"
                    },
                    legend: {
                        orient: 'vertical',
                        left: 'left',
                        y:'bottom',
                        data: ['电耗','油耗','气耗']
                    },
                    toolbox: {
                        show : true,
                        feature : {
                            mark : {show: true},                 
                            dataView : {show: true, readOnly: false},
                            saveAsImage : {show: true},
                        }
                    },
                    series : [
                        {
                            type: 'pie',
                            radius : '80%',
                            center: ['50%', '50%'],
                            data:[],
                            roseType: 'area',
                            calculable : true,
                            label:{
                                normal:{
                                    testStyle:{
                                        color: 'rgba(255, 255, 255, 0.5)'
                                    }
                                }
                            },
                            labelLine: {
                                 normal: {
                                    smooth: 0.2,
                                    length: 10,
                                    length2: 20
                                }
                            
                            },
                            data:[
                                {value:111,name:'电耗'},
                                {value:350,name:'油耗'},
                                {value:138,name:'气耗'},
                            ]
                            
                        }
                    ]
                };
                transport2.setOption(option);
            },
            drawtransport3(){
                let transport3 = echarts.init(document.getElementById('transport3'));
                let option={
                    title:{
                        text: '出租车能耗结构图',
                        x: 'center'
                    },
                    tooltip : {
                        trigger: 'item',
                        formatter: "{a} <br/>{b} : {c} ({d}%)"
                    },
                    legend: {
                        orient: 'vertical',
                        left: 'left',
                        y:'bottom',
                        data: ['电耗','油耗','气耗']
                    },
                    toolbox: {
                        show : true,
                        feature : {
                            mark : {show: true},                 
                            dataView : {show: true, readOnly: false},
                            saveAsImage : {show: true},
                        }
                    },
                    series : [
                        {
                            type: 'pie',
                            radius : '80%',
                            center: ['50%', '50%'],
                            data:[],
                            roseType: 'area',
                            calculable : true,
                            label:{
                                normal:{
                                    testStyle:{
                                        color: 'rgba(255, 255, 255, 0.5)'
                                    }
                                }
                            },
                            labelLine: {
                                 normal: {
                                    smooth: 0.2,
                                    length: 10,
                                    length2: 20
                                }
                            
                            },
                            data:[
                                {value:111,name:'电耗'},
                                {value:150,name:'油耗'},
                                {value:138,name:'气耗'},
                            ]
                            
                        }
                    ]
                };
                transport3.setOption(option);
            },
            drawCharts(){
                this.drawcity1()
                this.drawcity2()
                this.drawcity3()
                this.drawcity4()
                this.drawcity5()
                this.drawcity6()
                this.drawcity7()
                this.drawcity8()
                this.drawcity9()
                this.drawcity10()
                this.drawcity11()
                this.drawindustry1()
                this.drawindustry2()
                this.drawindustry3()
                this.drawtransport1()
                this.drawtransport2()
                this.drawtransport3()
            }
        },
        mounted: function () {
            this.drawCharts();
        },
        updated: function () {
            this.drawCharts()
        } 
    }
</script>


<style scoped lang="scss">
    .chart {
        width: 100%;
        float: left;
        .chart-container{
             background-color: #F2F2F2; 
            .chart-header{
                float: right;
                margin-bottom: 10px;
                margin-right: 20px;
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
</style>