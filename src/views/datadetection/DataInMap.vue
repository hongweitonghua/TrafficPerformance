<template>
    <section class="chart">
        <el-row>
            <el-col>
                <div id="energyDatainHeatMap" style="width:1000px;height:500px;" class = "chart-container">
                    
                </div>
            </el-col>
        </el-row>
    </section>
</template>

<script > 
    export default{
        /*components: {
            'remote-js': {
                render(createElement) {
                    return createElement('script', { attrs: { type: 'text/javascript', src: this.src }});
                },
                props: {
                    src: { type: String, required: true },
                },
            },
        },*/
        methods:{
            initMap(){                        
                window.maplet = new Maplet('energyDatainHeatMap');
                window.maplet.centerAndZoom(new MPoint(121.0,29.7), 5);  
                window.maplet.addControl(new MStandardControl());               
            }
        },
        mounted: function () {
            this.initMap();
        },
        updated: function () {
            
        }

    }
    /*import echarts from 'echarts'
    require('echarts/extension/bmap/bmap')
    export default{
        methods:{
            drawEnergyDatainHeatMap(){
                var dom = document.getElementById("energyDatainHeatMap");
                var myChart = echarts.init(dom);
                var app = {};
                var option = null;
                app.title = '热力图与百度地图扩展';
                console.log("lalalal");
                $.get('./api/hangzhouTrack', function (res) {
                    var data =res.data;
                    var points = [].concat.apply([], data.map(function (track) {
                        return track.map(function (seg) {
                            return seg.coord.concat([1]);
                        });
                    }));
                    myChart.setOption(option = {
                        animation: false,
                        bmap: {
                            center: [120.13066322374, 30.240018034923],
                            zoom: 14,
                            roam: true
                        },
                        visualMap: {
                            show: false,
                            top: 'top',
                            min: 0,
                            max: 5,
                            seriesIndex: 0,
                            calculable: true,
                            inRange: {
                                color: ['blue', 'blue', 'green', 'yellow', 'red']
                            }
                        },
                        series: [{
                            type: 'heatmap',
                            coordinateSystem: 'bmap',
                            data: points,
                            pointSize: 5,
                            blurSize: 6
                        }]
                    });
                    if (!app.inNode) {
                        // 添加百度地图插件
                        var bmap = myChart.getModel().getComponent('bmap').getBMap();
                        bmap.addControl(new BMap.MapTypeControl());
                    }
                });

                if (option && typeof option === "object") {
                    myChart.setOption(option, true);
                }
            },   
            drawCharts(){
                this.drawEnergyDatainHeatMap()
            }   
        },
        mounted: function () {
            this.drawCharts();
        },
        updated: function () {
            this.drawCharts()
        }
    }*/

</script>



<style scoped lang="scss">
    .chart {
        width: 100%;
        .chart-container{
            background-color: #F2F2F2; 
            
            
        }
        
    }
    /*.chart div {
        height: 400px;
        float: left;
    }*/

    .el-col {
        padding: 20px 20px;
    }
</style>