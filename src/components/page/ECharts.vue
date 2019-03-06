<template>
    <div>
        <div class="container">
            <div style="height:100%;width:100%">
                <el-row :gutter="10" type="flex" class="row-bg" justify="start">
                    <el-col :span="6">
                        <div  class="grid-a-contentWidth" >
                            <div class="residual echart-location" >
                                <chart :options="option_pie" style="width:100%;height:200%"></chart>
                            </div>
                        </div>
                        <br>
                        <div class="grid-a-contentWidth">
                            <div class="residual echart-location">
                                <chart :options="options_ring_offline" style="width:100%;height:200%"></chart>
                            </div>
                        </div>
                        <br>
                    </el-col>

                    <el-col :span="12">
                        <div class="grid-a-content-a-Width">

                            <el-input type="text" v-model="select_word" placeholder="筛选关键词" class="handle-input mr10"
                                      @change="select_word_change" size="mini"></el-input>
                            <el-button type="primary" icon="search" @click="search" size="mini">搜索</el-button>
                            <el-button type="info" icon="el-icon-refresh" circle @click="refreshPage"></el-button>

                            <div style="margin-top: 10px;margin-left: 20px;">
                                <el-radio-group v-model="device_status"  size="mini">
                                    <el-radio-button label="在线"></el-radio-button>
                                    <el-radio-button label="离线"></el-radio-button>
                                    <el-radio-button label="异常"></el-radio-button>
                                    <el-radio-button label="故障"></el-radio-button>
                                </el-radio-group>
                            </div>

                            <div class="radio_row"  style="width:100%;height:100%;">
                                <div class="radio_item"
                                     :class="curSelect == radio.id ? 'select_item' : ''"
                                     v-for="(radio, index) in data_radios"
                                     :key="index"
                                     >
                                    <el-checkbox class="checkbox-custom"  v-model="radio.status" @change="SelectItem(radio)"></el-checkbox>

                                    <el-badge :value="200" :max="199" class="item" @click="clickBadge(index)">

                                    <el-dropdown>
                                        <img :src="radio.img_src" class="el-dropdown-link" width="40" height="40" @click="clickItem(index)"
                                             onmouseover="onMouseOver">
                                        <el-dropdown-menu slot="dropdown">
                                            <el-dropdown-item @click.native="getProperty">属性</el-dropdown-item>
                                            <el-dropdown-item>应用控制</el-dropdown-item>
                                            <el-dropdown-item>删除</el-dropdown-item>
                                            <el-dropdown-item disabled></el-dropdown-item>
                                        </el-dropdown-menu>
                                    </el-dropdown>
                                    </el-badge>

                                    <div>
                                        <el-button style="width:40%;height:20%;" type="success"   circle size="mini">12</el-button>
                                        <el-button style="width:40%;height:20%;" type="danger"   circle size="mini">12</el-button>
                                    </div>

                                    <el-progress class="el_progress" :text-inside="false" style="width:80%;height:20%;" :percentage="57.2" color="#42b983"></el-progress>
                                    <el-progress class="el_progress" :text-inside="false" style="width:80%;height:20%;" :percentage="29.8" color="#f1e05a"></el-progress>

                                  {{radio.name}}

                                </div>
                            </div>
                            <el-button class="batch-btn" type="primary" icon="search" @click="batchOperation">批量操作</el-button>
                        </div>
                    </el-col>

                        <el-col :span="6">
                        <div class="grid-a-contentWidth">
                            <div class="residual echart-location" >
                                <chart :options="option_rose_excep" style="width:100%;height:200%"></chart>
                            </div>
                        </div>
                        <br>
                        <div class="grid-a-contentWidth">
                            <div class="residual echart-location" >
                                <chart :options="options_ring_warn" style="width:100%;height:200%"></chart>
                            </div>
                        </div>
                        <br>
                    </el-col>
                </el-row>

                <el-row :gutter="10" type="flex" class="row-bg" justify="start">
                    <el-col :span="8">
                        <div class="grid-a-contentWidth">
                            <div class="residual echart-location" >
                            <chart :options="option_histogram_d" style="width:100%;height:200%"></chart>
                            </div>
                        </div>
                        <br>
                    </el-col>
                    <el-col :span="8">
                        <div class="grid-a-contentWidth">
                            <div class="residual echart-location" >
                                <chart :options="option_area" style="width:100%;height:200%"></chart>
                            </div>
                        </div>
                        <br>
                    </el-col>
                    <el-col :span="8">
                        <div class="grid-a-contentWidth">
                            <div class="residual echart-location" >
                                <chart :options="option_area" style="width:100%;height:200%"></chart>
                            </div>
                        </div>
                        <br>
                    </el-col>
                </el-row>
                <br>
            </div>
        </div>
    </div>
</template>

<script>
    import VueGridLayout from 'vue-grid-layout';
    import echarts from 'echarts'


    var testLayout = [
        {"x":0,"y":0,"w":2,"h":2,"i":"0"},
        {"x":2,"y":0,"w":2,"h":4,"i":"1"},
        {"x":4,"y":0,"w":2,"h":5,"i":"2"},
        {"x":6,"y":0,"w":2,"h":3,"i":"3"},
        {"x":8,"y":0,"w":2,"h":3,"i":"4"},
        {"x":10,"y":0,"w":2,"h":3,"i":"5"},
        {"x":0,"y":5,"w":2,"h":5,"i":"6"},
        {"x":2,"y":5,"w":2,"h":5,"i":"7"},
        {"x":4,"y":5,"w":2,"h":5,"i":"8"},
        {"x":6,"y":4,"w":2,"h":4,"i":"9"},

    ];

    export default {

        data: () => ({
            layout: testLayout,
            data1:[
                {name:'2012',value:1141},
                {name:'2013',value:1499},
                {name:'2014',value:2260},
                {name:'2015',value:1170},
                {name:'2016',value:970},
                {name:'2017',value:1450}
            ],

            options_ring_offline: {
                title : {
                    text: '离线设备',
                    subtext: '',
                    x:'center',
                    y:'top'
                },
                tooltip : {
                    trigger: 'item',
                    formatter: "{a} <br/>{b} : {c} ({d}%)"
                },
                legend: {
                    orient : 'vertical',
                    x : 'left',
                    data: ['摄像头','门铃','烟雾报警器','开关','其他'],
                    // titleColor: '#ffffff',
                    fontColor: '#ffffff',
                    textStyle : {
                        fontSize : '10',
                        fontColor: '#ffffff',
                        fontWeight : 'bold'
                    }
                },
                toolbox: {
                    show : true,
                    feature : {
                        mark : {show: true},
                        dataView : {show: false, readOnly: false},
                        magicType : {
                            show: true,
                            type: ['pie', 'funnel'],
                            option: {
                                funnel: {
                                    x: '25%',
                                    width: '50%',
                                    funnelAlign: 'center',
                                    max: 1548
                                }
                            }
                        },
                        restore : {show: false},
                        saveAsImage : {show: false}
                    }
                },
                calculable : true,
                series : [
                    {
                        name:'离线设备',
                        type:'pie',
                        radius : ['35%', '55%'],
                        center: ['50%', '60%'],
                        itemStyle : {
                            normal : {
                                label : {
                                    show : false
                                },
                                labelLine : {
                                    show : false
                                }
                            },
                            emphasis : {
                                label : {
                                    show : true,
                                    position : 'center',
                                    textStyle : {
                                        fontSize : '10',
                                        fontColor: '#ffffff',
                                        fontWeight : 'bold'
                                    }
                                }
                            }
                        },
                        data:[
                            {value: 10, name: '摄像头'},
                            {value: 5, name: '门铃'},
                            {value: 15, name: '烟雾报警器'},
                            {value: 25, name: '开关'},
                            {value:102, name:'其他'}
                        ]
                    }
                ]
            },

            options_ring_warn: {
                title : {
                    text: '告警设备',
                    subtext: '',
                    x:'center',
                    y:'top'
                },
                tooltip : {
                    trigger: 'item',
                    formatter: "{a} <br/>{b} : {c} ({d}%)"
                },
                legend: {
                    orient : 'vertical',
                    x : 'left',
                    data: ['摄像头','门铃','烟雾报警器','开关','其他'],
                    // titleColor: '#ffffff',
                    fontColor: '#ffffff',
                    textStyle : {
                        fontSize : '10',
                        fontColor: '#ffffff',
                        fontWeight : 'bold'
                    }
                },
                toolbox: {
                    show : true,
                    feature : {
                        mark : {show: true},
                        dataView : {show: false, readOnly: false},
                        magicType : {
                            show: true,
                            type: ['pie', 'funnel'],
                            option: {
                                funnel: {
                                    x: '25%',
                                    width: '50%',
                                    funnelAlign: 'center',
                                    max: 1548
                                }
                            }
                        },
                        restore : {show: false},
                        saveAsImage : {show: false}
                    }
                },
                calculable : true,
                series : [
                    {
                        name:'告警设备',
                        type:'pie',
                        radius : ['35%', '55%'],
                        center: ['50%', '60%'],
                        itemStyle : {
                            normal : {
                                label : {
                                    show : false
                                },
                                labelLine : {
                                    show : false
                                }
                            },
                            emphasis : {
                                label : {
                                    show : true,
                                    position : 'center',
                                    textStyle : {
                                        fontSize : '10',
                                        fontColor: '#ffffff',
                                        fontWeight : 'bold'
                                    }
                                }
                            }
                        },
                        data:[
                            {value: 10, name: '摄像头'},
                            {value: 5, name: '门铃'},
                            {value: 15, name: '烟雾报警器'},
                            {value: 25, name: '开关'},
                            {value:102, name:'其他'}
                        ]
                    }
                ]
            },

            option_pie: {
                title : {
                    text: '设备状态',
                    subtext: '',
                    x:'center',
                    y:'top'
                },
                tooltip : {
                    trigger: 'item',
                    formatter: "{a} <br/>{b} : {c} ({d}%)"
                },
                toolbox: {
                    show : true,
                    feature : {
                        mark : {show: true},
                        dataView : {show: false, readOnly: false},
                        magicType : {
                            show: true,
                            type: ['pie', 'funnel'],
                            option: {
                                funnel: {
                                    x: '25%',
                                    width: '50%',
                                    funnelAlign: 'left',
                                    max: 1548
                                }
                            }
                        },
                    }
                },
                calculable : true,
                series : [
                    {
                        name:'设备状态',
                        type:'pie',
                        radius : '55%',
                        center: ['50%', '60%'],
                        data:[
                            {value:335, name:'在线'},
                            {value:310, name:'离线'},
                            {value:234, name:'异常'},
                            {value:135, name:'警告'},
                        ]
                    }
                ]
            },

            option_ring:
                {
                    tooltip : {
                        trigger: 'item',
                        formatter: "{a} <br/>{b} : {c} ({d}%)"
                    },
                    legend: {
                        orient : 'vertical',
                        x : 'left',
                        data:['直达','营销广告','搜索引擎','邮件营销','联盟广告',]
                    },
                    toolbox: {
                        show : true,
                        feature : {
                            mark : {show: true},
                            dataView : {show: false, readOnly: false},
                            magicType : {
                                show: true,
                                type: ['pie', 'funnel']
                            },
                            restore : {show: false},
                            saveAsImage : {show: false}
                        }
                    },
                    calculable : false,
                    series : [
                        {
                            name:'访问来源',
                            type:'pie',
                            // radius : [100, 140],
                            radius : '55%',
                            // for funnel
                            x: '60%',
                            width: '35%',
                            funnelAlign: 'left',
                            max: 1048,
                            data:[
                                {value:335, name:'直达'},
                                {value:310, name:'邮件营销'},
                                {value:234, name:'联盟广告'},
                                {value:135, name:'视频广告'},
                                {value:1048, name:'百度'},
                                {value:251, name:'谷歌'},
                                {value:147, name:'必应'},
                                {value:102, name:'其他'}
                            ]
                        }
                    ]
                },

            option_rose_excep:
                {
                    title: {
                        text: '异常设备',
                        subtext: '',
                        x: 'center',
                        y:'top'
                    },
                    tooltip: {
                        trigger: 'item',
                        formatter: "{a} <br/>{b} : {c} ({d}%)"
                    },
                    toolbox: {
                        show: true,
                        feature: {
                            mark: {show: true},
                            dataView: {show: false, readOnly: false},
                            magicType: {
                                show: true,
                                type: ['pie', 'funnel']
                            },
                            restore: {show: false},
                            saveAsImage: {show: false}
                        }
                    },
                    calculable: true,
                    series: [
                        {
                            name: '异常设备',
                            type: 'pie',
                            radius: [30, 80],
                            // center: ['50%', '50%'],
                            // radius : '55%',
                            center: ['50%', '60%'],
                            roseType: 'area',
                            x: '50%',               // for funnel
                            max: 20,                // for funnel
                            sort: 'ascending',     // for funnel
                            data: [
                                {value: 10, name: '摄像头'},
                                {value: 5, name: '门铃'},
                                {value: 15, name: '烟雾报警器'},
                                {value: 25, name: '开关'},
                            ]
                        }
                    ]
                },

            option_histogram_m: {
                title : {
                    text: '月告警次数',
                    subtext: ''
                },
                tooltip : {
                    trigger: 'axis'
                },
                legend: {
                    data:['次数',]
                },
                toolbox: {
                    show : true,
                    feature : {
                        mark : {show: true},
                        dataView : {show: false, readOnly: false},
                        magicType : {show: false, type: ['line', 'bar']},
                        restore : {show: false},
                        saveAsImage : {show: false}
                    }
                },
                calculable : true,
                xAxis : [
                    {
                        type : 'category',
                        data : ['1月','2月','3月','4月','5月','6月','7月','8月','9月','10月','11月','12月']
                    }
                ],
                yAxis : [
                    {
                        type : 'value'
                    }
                ],
                series : [
                    {
                        name:'次数',
                        type:'bar',
                        data:[2, 4, 7, 23, 25, 76, 135, 182, 32, 20, 6, 3],
                        markPoint : {
                            data : [
                                {type : 'max', name: '最大值'},
                                {type : 'min', name: '最小值'}
                            ]
                        },
                        markLine : {
                            data : [
                                {type : 'average', name: '平均值'}
                            ]
                        }
                    },
                ]
            },

            option_histogram_d: {
                title : {
                    text: '天告警次数',
                    subtext: ''
                },
                tooltip : {
                    trigger: 'axis'
                },
                legend: {
                    data:['次数',]
                },
                toolbox: {
                    show : true,
                    feature : {
                        mark : {show: true},
                        dataView : {show: false, readOnly: false},
                        magicType : {show: false, type: ['line', 'bar']},
                        restore : {show: false},
                        saveAsImage : {show: false}
                    }
                },
                calculable : true,
                xAxis : [
                    {
                        type : 'category',
                        data : ['1','2','3','4','5','6','7','8','9','10','11','12', '13', '14', '15', '16', '17', '18', '19', '20', '21', '22', '23']
                    }
                ],
                yAxis : [
                    {
                        type : 'value'
                    }
                ],
                series : [
                    {
                        name:'次数',
                        type:'bar',
                        data:[2, 4, 7, 23, 25, 76, 135, 182, 32, 20, 6, 3],
                        markPoint : {
                            data : [
                                {type : 'max', name: '最大值'},
                                {type : 'min', name: '最小值'}
                            ]
                        },
                        markLine : {
                            data : [
                                {type : 'average', name: '平均值'}
                            ]
                        }
                    },
                ]
            },
            option_area: {
                title : {
                    text: '月告警次数',
                    subtext: ''
                },
                tooltip : {
                    trigger: 'axis'
                },
                legend: {
                    data:['次数']
                },
                toolbox: {
                    show : true,
                    feature : {
                        mark : {show: true},
                        dataView : {show: false, readOnly: false},
                        magicType : {show: false, type: ['line', 'bar', 'stack', 'tiled']},
                        restore : {show: false},
                        saveAsImage : {show: false}
                    }
                },
                calculable : true,
                xAxis : [
                    {
                        type : 'category',
                        boundaryGap : false,
                        data : ['1月','2月','3月','4月','5月','6月','7月','8月','9月','10月','11月','12月']
                    }
                ],
                yAxis : [
                    {
                        type : 'value'
                    }
                ],
                series : [
                    {
                        name:'次数',
                        type:'line',
                        smooth:true,
                        itemStyle: {normal: {areaStyle: {type: 'default'}}},
                        data:[12, 21, 54, 260, 830, 710, 899, 990, 1234, 123, 345, 678]
                    },
                ]
            },

            checkbox_status: false,

            data_radios:
                [{
                    id:0,
                    name:'192.168.10.12',
                    status: false,
                    img_src: require('../../../static/img/mac_status_online.png')
                },
                    {
                        id:1,
                        name:'192.168.10.13',
                        status: false,
                        img_src:require('../../../static/img/mac_status_online.png')
                    },
                    {
                        id:2,
                        name:'192.168.10.14',
                        status: false,
                        img_src:require('../../../static/img/mac_status_online.png')
                    },
                    {
                        id:3,
                        name:'192.168.10.15',
                        status: false,
                        img_src:require('../../../static/img/mac_status_online.png')
                    },
                    {

                        id:4,
                        name:'192.168.10.16',
                        status: false,
                        img_src:require('../../../static/img/mac_status_online.png')
                    },
                    {
                        id:5,
                        name:'192.168.10.17',
                        status: false,
                        img_src:require('../../../static/img/mac_status_online.png')
                    },{
                    id:6,
                    name:'192.168.10.18',
                    status: false,
                    img_src:require('../../../static/img/mac_status_online.png')
                },
                    {
                        id:7,
                        name:'192.168.10.19',
                        status: false,
                        img_src:require('../../../static/img/mac_status_online.png')
                    },
                    {
                        id:7,
                        name:'192.168.10.20',
                        status: false,
                        img_src:require('../../../static/img/mac_status_online.png')
                    },


                ],
            curSelect:null,
            select_word: '',
            device_status: '',



        }),

        components: {
            GridLayout: VueGridLayout.GridLayout,
            GridItem: VueGridLayout.GridItem,
        },

        methods: {
            clickItem(index) {
                alert(index);
            },

            clickBadge(index) {
                alert("clickBadge");
            },

            SelectItem(radio) {
                alert("SelectItem");
                console.log(radio)
                console.log(radio.status)

            },
            search() {
                alert("search");
            },

            checkedItem(index) {
                alert("checkedItem");
                console.log(index)
            },
            getProperty() {
                alert("getProperty");
            },

            touchStart(e){
                alert("touchStart");
                console.log(e);
                var that = this;
                var list = that.data_radios;
                for (var i = 0, len = list.length; i < len; ++i) {
                    if (list[i].id == e) {
                        that.curSelect = i;
                    }
                }
            },
            touchEnd() {
                var that = this;
                that.curSelect = null;
            },

            batchOperation() {
                alert("批量操作");
            },

            onMouseOver() {
                alert("onMouseOver");
            },

            refreshPage() {
                alert("refreshPage");
            },
            select_word_change() {
                alert("select_word_change");
            },
        },


    }
</script>

<style scoped>
    .grid-a-contentWidth {
        /*background-color: rgb(44, 143, 121); blue:#003366 */
        background-color: #b3d8ff;
        border-radius: 4px;
        min-height: 240px;
        align-items: center;
        /*实现水平居中*/
        justify-content: center;
    }

    .grid-a-content-a-Width {
        /*background-color: rgb(44, 143, 121);*/
        background-color: #b3d8ff;
        border-radius: 4px;
        min-height: 480px;
    }

    .echart-location {
        align-items: center;
        /*实现水平居中*/
        justify-content: center;
    }

    .grid-one-contentheight {
        background-color: rgb(44, 143, 121);
        min-height: 100%;
    }

    .el-row-two {
        margin-bottom: 80px;
        margin-top: 80px;
    }

    .radio_row {
        display: flex;
        align-items: center;
        width:100%;
        overflow: auto;
        flex-wrap: wrap;
        max-height: 365px;
    }
    .radio_item {
        display: flex;
        justify-content: center;
        align-items: center;
        flex-direction: column;
        width:25%;
        max-height: 200px;
        padding-top: 10px;
        padding-bottom: 10px;
        /*border: 1px solid #00a854;*/
    }
    .select_item {
        opacity: 0.4;
        background: #e5e5e5;
    }

    .radio_item img{
        margin-bottom: 3px;
        display: block;
    }

    .batch-btn {
        padding-top: 10px;
        padding-left: 10px;
        margin-top: 10px;
        margin-left: 20px;
        margin-bottom: 10px;
    }

    .handle-input {
        width: 300px;
        display: inline-block;
        margin-left: 20px;
        margin-top: 10px;
    }
    .checkbox-custom {
        margin-right: 68px;
    }

    .popover-custom {
        margin-right: 68px;
    }

    . el_progress {
        /*justify-content: center;*/
        align-items: center;
        margin-left: 40px;
    }

    .handle-input {
        width: 160px;
        display: inline-block;
    }

    .el-carousel__item h3 {
        color: #475669;
        font-size: 18px;
        opacity: 0.75;
        line-height: 300px;
        margin: 0;
    }

    .el-carousel__item:nth-child(2n) {
        background-color: #99a9bf;
    }

    .el-carousel__item:nth-child(2n+1) {
        background-color: #d3dce6;
    }

</style>
