---
uuid: 67851f89-296d-7067-63c0-e2a0dfb740c3
title: Echarts画图实践
date: 2020-11-13 15:52:54
categories: 技术
tags: 
- Echarts
---
> ECharts，一个使用 JavaScript 实现的开源可视化库，可以流畅的运行在 PC 和移动设备上。最近一直在做大屏数据可视化，用到最多踩坑最多的就是ECharts了。因此记录一下期间利用ECharts画的各种图。
## 准备工作
1. 安装ECharts
``` js
npm install echarts --save
```
2. 安装vue-count-to
``` js
npm install vue-count-to
```
全局引入组件
``` js
import countTo from 'vue-count-to';
Vue.component('countTo', countTo);
```

## 滚动进度条
<div align=center>
    <img src="percent.gif" />
</div>

需求：
- 进度条有背景，内有边距轮廓
- 进度条颜色渐变
- 进度条和值定时滚动

混合mixins.js
``` js
export const mixinMethods = {
    data() {
        return {
            timerList:[]
        };
    },
    mounted() {
        // let randomTimer = Math.floor(Math.random() * 10) + 10;
        const timer = setInterval(() => {
            this.timeKey = new Date().getTime();
        }, 5000);
        this.timerList.push(timer);
    },
    methods: {
        // 清空定时器
        clearTimer() {
            this.timerList.forEach(item => {  
                clearInterval(item);
            });
        },
    },
};
```
组件Process.vue
``` js
<template>
    <section class="process-area flex flex-ac">
        <div class="title">
            <p class="main">
                <span>{{ title }}</span
                >近视人数
            </p>
            <p class="tag">{{ desc }}</p>
        </div>
        <div class="num-show">
            <span :style="{ color: color }">
                <countTo
                    :style="{ color: color }"
                    :key="timeKey"
                    :separator="','"
                    :endVal="num || 0"
                    :duration="1500"
                ></countTo>
            </span>
        </div>
        <div class="view" ref="barView"></div>
    </section>
</template>

<script>
// // 引入 ECharts 主模块
let echarts = require('echarts/lib/echarts');
// // 引入饼图
require('echarts/lib/chart/bar');
import { mixinMethods } from '../../../mixins/mixins.js';
export default {
    mixins: [mixinMethods],
    data() {
        return {
            timeKey: new Date().getTime(),
        };
    },
    props: {
        title: {
            type: String,
        },
        desc: {
            type: String,
        },
        percent: {
            type: Number,
            default: 0,
        },
        num: {
            type: Number,
            default: 0,
        },
        color: {
            type: String,
            default: '#FAA810',
        },
    },
    created() {},
    mounted() {
        this.echartsBarInit();
        setInterval(() => {
            this.echartsBarInit();
        }, 5000);
    },
    methods: {
        echartsBarInit() {
            let chart = echarts.init(this.$refs.barView); // 初始化echarts实例
            chart.clear();
            chart.setOption(
                // 通过setOption来生成柱状图
                {
                    grid: {
                        // 直角坐标系内绘图网格
                        left: '20', //grid 组件离容器左侧的距离,
                        //left的值可以是80这样具体像素值，
                        //也可以是'80%'这样相对于容器高度的百分比
                        top: '40',
                        right: '100',
                        bottom: '0',
                        containLabel: true, //gid区域是否包含坐标轴的刻度标签。为true的时候，
                        // left/right/top/bottom/width/height决定的是包括了坐标轴标签在内的
                        //所有内容所形成的矩形的位置.常用于【防止标签溢出】的场景
                    },
                    xAxis: {
                        //直角坐标系grid中的x轴,
                        //一般情况下单个grid组件最多只能放上下两个x轴,
                        //多于两个x轴需要通过配置offset属性防止同个位置多个x轴的重叠。
                        type: 'value', //坐标轴类型,分别有：
                        //'value'-数值轴；'category'-类目轴;
                        //'time'-时间轴;'log'-对数轴
                        splitLine: { show: false }, //坐标轴在 grid 区域中的分隔线
                        axisLabel: { show: false }, //坐标轴刻度标签
                        axisTick: { show: false }, //坐标轴刻度
                        axisLine: { show: false }, //坐标轴轴线
                    },
                    yAxis: {
                        type: 'category',
                        axisTick: { show: false },
                        axisLine: { show: false },
                        axisLabel: {
                            color: '#fff',
                            fontSize: 14 * window.REM_MULTIPLE,
                            //设置字数限制
                            formatter: function (value) {
                                if (value.length > 5) {
                                    return value.substring(0, 5) + '...';
                                } else {
                                    return value;
                                }
                            },
                        },
                        data: [''], //类目数据，在类目轴（type: 'category'）中有效。
                        //如果没有设置 type，但是设置了axis.data,则认为type 是 'category'。
                    },
                    series: [
                        //系列列表。每个系列通过 type 决定自己的图表类型
                        {
                            name: '%', //系列名称
                            type: 'bar', //柱状、条形图
                            barWidth: 12 * window.REM_MULTIPLE, //柱条的宽度,默认自适应
                            data: [this.percent], //系列中数据内容数组
                            // label: { //图形上的文本标签
                            //     show: true,
                            //     position: 'right',//标签的位置
                            //     // offset: [0,-40],  //标签文字的偏移，此处表示向上偏移40
                            //     formatter: '{c}',//标签内容格式器 {a}-系列名,{b}-数据名,{c}-数据值
                            //     color: '#67FFFF',//标签字体颜色
                            //     fontSize: 14  //标签字号
                            // },
                            itemStyle: {
                                //图形样式
                                normal: {
                                    //normal 图形在默认状态下的样式;
                                    //emphasis图形在高亮状态下的样式
                                    barBorderRadius: 10, //柱条圆角半径,单位px.
                                    //此处统一设置4个角的圆角大小;
                                    //也可以分开设置[10,10,10,10]顺时针左上、右上、右下、左下
                                    color: new echarts.graphic.LinearGradient(
                                        0,
                                        0,
                                        1,
                                        0,
                                        [
                                            {
                                                offset: 0,
                                                color: '#FEF302', //柱图渐变色起点颜色
                                            },
                                            {
                                                offset: 1,
                                                color: this.color, //柱图渐变色终点颜色
                                            },
                                        ]
                                    ),
                                },
                            },
                            zlevel: 1, //柱状图所有图形的 zlevel 值,
                            //zlevel 大的 Canvas 会放在 zlevel 小的 Canvas 的上面
                        },
                        {
                            name: 'aa',
                            type: 'bar',
                            barGap: '-124%', //不同系列的柱间距离，为百分比。
                            // 在同一坐标系上，此属性会被多个 'bar' 系列共享。
                            // 此属性应设置于此坐标系中最后一个 'bar' 系列上才会生效，
                            //并且是对此坐标系中所有 'bar' 系列生效。
                            // barCategoryGap:'50%',
                            barWidth: 18 * window.REM_MULTIPLE,
                            animation: false,
                            data: [100],
                            color: '#005982', //柱条颜色
                            itemStyle: {
                                //图形样式
                                normal: {
                                    //normal 图形在默认状态下的样式;
                                    //emphasis图形在高亮状态下的样式
                                    barBorderRadius: 10, //柱条圆角半径,单位px.
                                    //此处统一设置4个角的圆角大小;
                                    //也可以分开设置[10,10,10,10]顺时针左上、右上、右下、左下
                                    color: new echarts.graphic.LinearGradient(
                                        0,
                                        0,
                                        1,
                                        0,
                                        [
                                            {
                                                offset: 0,
                                                color: '#F0F0F0', //柱图渐变色起点颜色
                                            },
                                            {
                                                offset: 1,
                                                color: '#F0F0F0', //柱图渐变色终点颜色
                                            },
                                        ]
                                    ),
                                },
                            },
                            label: {
                                //图形上的文本标签
                                show: true,
                                position: 'right', //标签的位置
                                offset: [20, 0], //标签文字的偏移，此处表示向上偏移40
                                // formatter: '{c}',//标签内容格式器 {a}-系列名,{b}-数据名,{c}-数据值
                                formatter: (series) => {
                                    let dataList = [this.percent + '%'];
                                    return dataList[series.dataIndex];
                                },
                                color: '#2E2E2E', //标签字体颜色
                                fontSize: 27 * window.REM_MULTIPLE, //标签字号
                            },
                        },
                    ],
                }
            );
            //根据窗口的大小变动图表 --- 重点
            window.addEventListener('resize', () => {
                chart.resize();
            });
        },
    },
    components: {},
    destroyed() {
        this.clearTimer();
    },
};
</script>
<style lang="less">
.process-area {
    .el-progress-bar__inner {
        background-color: unset;
        background-image: linear-gradient(to left, #fec107, #fef302);
    }
    .el-progress__text {
        font-size: 27px;
        font-weight: bold;
        color: #2e2e2e;
        line-height: 35px;
        margin-left: 10px;
    }
    .el-progress-bar {
        padding-right: 80px !important;
    }
}
</style>
<style lang="less" scoped>
.process-area {
    .view {
        width: 600px;
        height: 60px;
    }
    .el-progress-bar__inner {
        background-color: unset;
        background-image: linear-gradient(to left, #fec107, #fef302);
    }
    .el-progress__text {
        font-size: 27px;
        font-weight: bold;
        color: #2e2e2e;
        line-height: 35px;
        margin-left: 10px;
    }
    .el-progress-bar {
        padding-right: 80px !important;
    }
}
</style>
<style lang="less" scoped>
.process-area {
    position: relative;
    margin-bottom: 35px;
    .num-show {
        position: absolute;
        left: 230px;
        top: -5px;
        span {
            font-size: 32px;
            font-weight: bold;
            color: #faa810;
            line-height: 41px;
        }
    }
    .title {
        width: 170px;
        margin-right: 30px;
        .main {
            font-size: 18px;
            color: #333333;
            margin-right: 49px;
            font-weight: 600;
            width: 100%;
            text-align: right;
            span {
                font-size: 24px;
                font-weight: 600;
            }
        }
        .tag {
            font-size: 15px;
            color: #999999;
            text-align: right;
            margin-top: 5px;
        }
    }
    .process {
        width: 450px;
        div {
            p {
                font-size: 32px;
                color: #faa810;
            }
        }
    }
}
</style>
```
使用Index.vue
``` js
import Process from './components/Process.vue';
export default {
    components: {
        Process
    }
}
<template>
    <div>
        <Process title="轻度" desc="近视50度-300度之间" :num="10000 :percent="30"></Process>
        <Process title="中度" desc="近视300度-600度之间" :num="8000" :percent="25" color="#F48520"></Process>
        <Process title="重度" desc="近视600度之间" :num="12000" :percent="35" color="#E84530"></Process>
    </div>
</template>
```