
<script>
    import { use } from 'echarts/core';
    import { CanvasRenderer } from 'echarts/renderers';
    import { LineChart ,BarChart,} from 'echarts/charts';
    // import{CategoryScale,
    //     ChartData,
    //     ChartOptions,
    //     BarController,
    //     BarElement,
    //     LinearScale,
    //     LineController,
    //     LineElement,}from'chaart.js';
    import {
        TitleComponent,
        TooltipComponent,
        LegendComponent,
    } from 'echarts/components';
    import VChart from 'vue-echarts';
    
    use([
        
        CanvasRenderer,
        LineChart,
        BarChart,
        TitleComponent,
        TooltipComponent,
        LegendComponent,
    ]);

    export default {
        data() {
            return {
                posts: [],
                option: {
                    title: {
                        text: '近十年電價變化趨勢及售電量合計',
                        left: 'center',
                    
                    },
                    tooltip: {
                        trigger: 'axis',
                        axisPointer: {
                        type: 'cross' //十字定軸
                        }
                    },
                    legend: {
                        itemWidth:15,
                        itemHeihjt:15,
                        data: ['平均電價','售電量合計'],//添加新的數據系列名稱
                        left: 'right',
                        
                    },
                    xAxis: {
                        name: '\n\n\n年度',
                        type: 'category',
                        data: [],
                        axisLabel: {
                            rotate: 45,
                        
                        
                        }
                    },
                    yAxis:[ {
                        type: 'value', //是數值型數據
                        name: '度/元(新台幣)'
                    },
                    {
                        type:'value',
                        name:'售電量合計(百億度)',//添加y軸單位
                        axisLabel:{
                            formatter:'{value}百億度',
                            
                        },//設置y軸刻度標籤的格式


                    }],
                    series: [{
                        name:'平均電價',
                        type: 'line', //指定這個數據系列以折線圖的形式呈現
                        data: [],//從數據中獲取的每年平均電價數據存入這個陣列中
                        label: {
                            show: true,//數據標籤將顯示在圖表中
                            position: 'top',//數據標籤將顯示在每個數據點的上
                            textStyle: {
                                fontSize: 16, // 設置字體大小
                            }
                            
                        },
                        color:"#2F5954", //折線顏色
                        shadowColor:"#E8FFF5",
                        shadowBlur:8,
                        borderColor:"#CCEC60",
                        symbolSize:12,//改變折線圖節點大小
                        
                        
                        
                    },
                {
                    name:'售電量合計',
                    type:'bar',//添加柱狀圖類型
                    yAxisIndex:1,//設置與第二個y軸相關
                    data:[],//新的系列數據
                    // 设置柱状图样式
                    itemStyle: {
                        color: '#CCEC60', // 柱状图颜色
                        // 陰影效果
                        shadowColor: '#31715C',
                        shadowBlur: 5,
                        // 設置柱状圖邊框样式
                        borderColor: 'none',
                        borderWidth: 1,
                    label: {
                        show: true, // 顯示長條圖標籤
                        position: 'insideTop', // 標籤位置（也可以是 'inside'、'insideTop' 等）
                        color: '#ADFEDC', // 標籤字體颜色
                        fontSize: 20, // 標籤字體大小
                        fontWeight: 'bold', // 標籤字體粗细
                        formatter: '{c}百億度', // 標籤内容格式化，{c} 表示數據值
                        },
                    }
                }]
                }
            }
        },
        methods: {
            fetchData() {
                fetch("../10years (1).json")
                    .then(res => res.json())
                    .then(data => {
                        this.posts = data;
                        const years = data.map(item => item['年度']);
                        const averagePrices = data.map(item => parseFloat(item['平均電價合計']).toFixed(2));//toFixed() 方法来實現四舍五入并保留指定位數的小數
                        const salesVolumes = data.map(item => (parseFloat(item['售電量合計'])/ 1e8).toFixed(2));//添加售電量合計數據
                        this.option.xAxis.data = years;//從資料中獲取的年份數據（即 years 變數）設置為圖表的 X 軸數據。
                        this.option.series[0].data = averagePrices;//將處理後的平均電價數據（即 averagePrices 變數）賦值給圖表配置選項中的第一個數據系列（series）的數據（data）屬性。代表了圖表中的第一組數據，並通常用於定義圖表中的第一條線、第一個柱形等
                        this.option.series[1].data=salesVolumes;//將售電量合計數據添加到第二個數據系列中   

                    })
            }
        },
        components: {
            VChart
        },
        //響應式頁面，讓圖表跟著網頁容器大小一起變動
        resizeChart(){
            this.$nextTick(()=>{
                this.$refs.chart.resize()
            })
        },
        mounted(){
            window.removeEventListener('reize',this.resizeChart)
        },
        beforeDestory(){
            window.removeEventListener('reize',this.resizeChart)
        }
    }


    
    </script>


<template>
    <div>
        <button class="btn" @click="fetchData()">點這裡看圖表</button>
        <!-- 將 Vue 實例中的 option 資料屬性綁定到了 <v-chart> 元件的 option 屬性上。這樣做可以將 Vue 實例中的圖表配置資料傳遞給 <v-chart> 元件，從而渲染出相應的圖表。 -->
        <v-chart class="chart" :option="option" ref="chart" />

        
    </div>
</template>

<style scoped>
    .chart {
        height: 600px;
        width: 80%;
        text-align: center;
        margin:auto;
    }
    .btn{
        width: 120px;
        height: 34px;
        font-size: 14px;
        font-weight: 700;
        background-color: #2F5954;
        border: none;
        color: white;
        transition: 0.3s;
        border-radius:10px;
        box-shadow: 3px 3px 0px 0px #c9d9cd;

        &:hover{
            background-color:#C09F60
        }
        &:active{
            scale: 0.8;
        }
    }


    
</style>
