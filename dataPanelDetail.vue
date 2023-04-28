<template>
    <div>
        <div class="topclass">
            <el-input style="width:350px" v-model="panelName" placeholder="请输入面板名称"></el-input>
            <el-input style="width:350px;margin: 20px 0;display: block;" v-model="soltData"
                placeholder="请输入面板排序"></el-input>
            <div v-for="(item, index) in allEventList" :key="index">
                <el-divider content-position="left">事件名</el-divider>
                <el-select filterable v-model="item.searchEventId" @change="selectEventChange(item.searchEventId, index)"
                    placeholder="请选择事件名">
                    <el-option v-for="item in eventList" :key="item.id" :label="item.remarks" :value="item.id">
                    </el-option>
                </el-select>
                <div v-if="item.searchEventId" class="shaiclass">
                    <span class="spanclass">的</span>
                    <!-- <el-select v-model="item.searchTermsEventId" placeholder="请选择筛选项">
                                                                            <el-option v-for="item in searchTermsArray" :key="item.label" :label="item.label"
                                                                                :value="item.name">
                                                                            </el-option>
                                                                        </el-select> -->
                    <el-cascader placeholder="请选择筛选项" v-model="item.searchTermsEventId" :options="searchTermsArray"
                        filterable />
                </div>
                <el-button type="text" v-if="index != 0" style="margin-left:10px" @click="handleAddEvent(false, index)"
                    icon="el-icon-close">删除事件</el-button>
                <el-button type="text" style="margin-left:10px" @click="handleAddVariable(true, index)"
                    icon="el-icon-plus">变量筛选</el-button>
                <div class="bianlinagall">
                    <div class="bianlinagLeft">
                        <div class="hehuoall" v-if="item.children.length > 1">
                            <div @click="item.queryType = 'and'"
                                :style="item.queryType == 'and' ? 'color:white;background-color:#409EFF' : ''"
                                class="hehuo">
                                且
                            </div>
                            <div @click="item.queryType = 'or'"
                                :style="item.queryType == 'or' ? 'color:white;background-color:#409EFF' : ''" class="hehuo">
                                或
                            </div>
                        </div>
                    </div>
                    <div class="bianliangRight">
                        <div v-for="(itemes, indexes) in item.children" :key="indexes">
                            <div class="topclass topclassChild">
                                <el-select filterable v-model="itemes.searchVariableId" no-data-text="请选择合适的事件"
                                    placeholder="请选择变量名">
                                    <el-option v-for="item in item.variableList" :key="item.id" :label="item.remarks"
                                        :value="item.id">
                                    </el-option>
                                </el-select>
                                <div v-if="itemes.searchVariableId" class="shaiclass">
                                    <el-select v-model="itemes.searchTermsVariableId" placeholder="请选择筛选项">
                                        <el-option v-for="item in searchTermsArray2" :key="item.value" :label="item.label"
                                            :value="item.name">
                                        </el-option>
                                    </el-select>
                                </div>
                                <div v-if="itemes.searchVariableId" class="shaiclass">
                                    <el-input v-model="itemes.searchValue" placeholder="请输入筛选值"></el-input>
                                </div>
                                <el-button type="text" style="margin-left:10px"
                                    @click="handleAddVariable(false, index, indexes)" icon="el-icon-close">删除变量</el-button>
                            </div>
                        </div>
                    </div>

                </div>

            </div>
            <el-radio-group style="margin-top: 20px;" v-model="eventType">
                <el-radio-button label="left">普通</el-radio-button>
                <el-radio-button label="right">平均值</el-radio-button>
            </el-radio-group>
            <el-button type="primary" style="margin-top:20px" @click="handleAddEvent(true, index)"
                icon="el-icon-plus">新增事件名</el-button>
        </div>
        <div class="topclass">
            <el-divider content-position="left">全局变量名</el-divider>
            <div v-for="(item, index) in   allFilterVariableList  " :key="index" style="margin-bottom:20px">
                <el-select filterable @clear="item.searchTermsVariable = ''; item.searchValue = ''" clearable
                    v-model="item.searchVariable" no-data-text="请选择合适的事件" placeholder="请选择变量名">
                    <el-option v-for="  item   in   eventVariableList  " :key=" item.id " :label=" item.remarks "
                        :value=" item.name ">
                    </el-option>
                </el-select>
                <div v-if=" item.searchVariable " class="shaiclass">
                    <span class="spanclass">的</span>
                    <el-select clearable v-model=" item.searchTermsVariable " placeholder="请选择筛选项">
                        <el-option v-for="  item   in   searchTermsArray2  " :key=" item.value " :label=" item.label "
                            :value=" item.name ">
                        </el-option>
                    </el-select>
                </div>
                <div v-if=" item.searchVariable " class="shaiclass">
                    <el-input clearable v-model=" item.searchValue " placeholder="请输入筛选值"></el-input>
                </div>
                <el-button type="text" v-if=" index != 0 " style="margin-left:10px" @click=" handleAddFilter(false, index) "
                    icon="el-icon-close">删除</el-button>
                <el-button v-if="
                    (index + 1 >=
                        allFilterVariableList.length)
                " type="text" style="margin-left:10px" @click=" handleAddFilter(true, index) "
                    icon="el-icon-plus">筛选</el-button>
            </div>
            <el-divider content-position="left">维度</el-divider>
            <el-select filterable clearable v-model=" dimensionList " no-data-text="请选择合适的事件" placeholder="请选择维度">
                <el-option v-for="  item   in   eventVariableList  " :key=" item.id " :label=" item.remarks "
                    :value=" item.name ">
                </el-option>
            </el-select>

            <el-divider content-position="left">日期</el-divider>
            <div style="margin-bottom: 20px;">
                <el-select no-data-text="请选择合适的事件" placeholder="请选择日期类型" v-model=" dateInterval " clearable>
                    <el-option-group v-for="  group   in   optionsTime  " :key=" group.label " :label=" group.label ">
                        <el-option v-for="  item   in   group.options  " :key=" item.value " :label=" item.label "
                            :value=" item.value " />
                    </el-option-group>
                </el-select>
            </div>

            <el-date-picker v-if=" dateInterval == 'staticDay' || dateInterval == 'staticHour' " @change=" datePickChange "
                v-model=" timeValue " value-format="YYYY-MM-DD HH:mm:ss" type="daterange" start-placeholder="开始时间"
                end-placeholder="结束时间" />
            <div v-if=" dateInterval == 'dynamicDay' ">
                <span>近</span> <el-input style="width:120px" v-model=" timeValue2 " placeholder="请输入天数"></el-input> 天
            </div>
            <div v-if=" dateInterval == 'dynamicHour' ">
                近 <el-input style="width:120px" v-model=" timeValue2 " placeholder="请输入小时数"></el-input> 小时
            </div>
            <div style="margin-top:20px">
                <div v-if=" !pageLoading ">
                    <el-button type="primary" :loading=" true " disabled>数据加载中...搜索暂不可用</el-button>
                </div>
                <div v-else>
                    <el-button type="primary" :loading=" loading " @click=" renderOptions ">{{
                        loading ? '查询中...' : '立即查询'
                        }}</el-button>
                    <el-button v-if=" operationType == 'add' " type="success" @click=" handleAddPanel ">新增面板</el-button>
                    <el-button v-if=" operationType == 'edit' " type="success" @click=" handleEditPanel ">保存面板</el-button>
                    <el-button type="warning" @click=" handleLastPage " plain>返回到面板列表</el-button>
                </div>
            </div>
        </div>
        <div class="topclass">
            <div class="colorObj">
                <el-collapse v-model=" activeNames ">
                    <el-collapse-item v-for="(  item, index  ) in   eventColorObj  " :key=" index " :name=" index ">
                        <template #title>
                            <div class="letterclass">
                                {{ letterArray[index] }}
                            </div>
                            {{ item.eventName }}({{ item.chinese }})
                        </template>
                        <div v-for="(  itemes, indexes  ) in   item.variableArray  " class="disp" :key=" indexes ">
                            <div :style=" 'background-color:' + itemes.color " class="yuan"></div>
                            <div>{{ itemes.variableName }}</div>
                        </div>
                    </el-collapse-item>
                </el-collapse>
            </div>

            <scEcharts ref="echartsRef" height="400px" style="margin:10px 0 30px 0" :option=" options "></scEcharts>

            <el-table :data=" seriesObj " border :span-method=" objectSpanMethod " style="width: 100%; margin-top: 20px">
                <el-table-column align="center" label="事件" width="251">
                    <template #default=" scope ">
                        <div>{{ scope.row.data.filter(e => e)[0].letter }}.{{
                            scope.row.data.filter(e => e)[0].event
                            }}({{ scope.row.data.filter(e => e)[0].chinese }})</div>
                    </template>
                </el-table-column>
                <el-table-column align="center" label="属性" width="180">
                    <template #default=" scope ">
                        <div>{{ Number(scope.row.name) ? Number(scope.row.name).toFixed(2) : (scope.row.name) }}</div>
                    </template>
                </el-table-column>
                <el-table-column align="center" label="总和" width="180">
                    <template #default=" scope ">
                        <div>{{ scope.row.sum }}</div>
                    </template>
                </el-table-column>
                <el-table-column align="center" v-for="(  item, index  ) in   consecutiveDaysArray  " :key=" index "
                    :label=" item " width="180">
                    <template #default=" scope ">
                        <div>{{ scope.row.data.find(e => e?.tab == item)?.value }}</div>
                    </template>
                </el-table-column>
            </el-table>
        </div>
    </div>
</template>
<script setup>
import { ref, onMounted, onBeforeMount, watch } from 'vue'
import scEcharts from '@/components/scEcharts';
import api from '@/api'
import { ElMessage, ElMessageBox } from 'element-plus'
import router from '@/router'
const eventType = ref('left')
// 选中的事件名
// const searchEvent = ref('')
// 筛选项内容（事件）
const searchTermsEvent = ref('')
// 选中的维度
const dimension = ref('')
// 事件名列表
const eventList = ref([])
// 所有事件对应的变量名列表
const eventVariableList = ref([])
// 日期
const timeValue = ref('')
const timeValue2 = ref('')
// 维度
const dimensionList = ref('')
// 随机定义20种颜色数组
const colorArray = ref([])
// 连续日期数组
const consecutiveDaysArray = ref([])
// 加载
const loading = ref(false)
// 操作类型
const operationType = ref('')
// 面板名称
const panelName = ref('')
// 全局变量名数组
const allFilterVariableList = ref([{
    searchVariable: '', //变量名
    searchTermsVariable: '', //筛选项
    searchValue: '', //筛选值
}])
// 排序
const soltData = ref('')
// 折叠面板
let activeNames = ref([])
// series数据
const seriesObj = ref([])
// echarts实例
const echartsRef = ref(null)
// 26个大写英文字母
const letterArray = ref(['A', 'B', 'C', 'D', 'E', 'F', 'G', 'H', 'I', 'J', 'K', 'L', 'M', 'N', 'O', 'P', 'Q', 'R', 'S', 'T', 'U', 'V', 'W', 'X', 'Y', 'Z'])
// 所有属性列表
const allVariableList = ref([])
// 面板下标
let panelIndex = ref(0)
// 事件类型
const dateInterval = ref('staticDay')
const panelId = ref('')
const optionsTime = ref([{
    label: '固定时间',
    options: [
        {
            value: 'staticDay',
            label: '按天',
        },
        {
            value: 'staticHour',
            label: '按小时',
        },
    ],
}, {
    label: '动态时间',
    options: [
        {
            value: 'dynamicDay',
            label: '按天',
        },
        {
            value: 'dynamicHour',
            label: '按小时',
        },
    ],
}])
//
// 事件颜色分类对象数组
/**
 * {
 *    eventName: '事件名',
 *    variableArray: [
 *       {
 *         variableName: '变量名',
 *         color: '颜色'
 *       }
 *    ]
 * }
 */
const eventColorObj = ref([])
// 所有事件列表
/**
 *   {
        searchEvent: '', //事件名
        searchTermsEventId: 'total', //筛选项
        children: [
            {
                searchVariable: '', //变量名
                searchTermsVariable: '', //筛选项
            }
        ]
    }
 */
const allEventList = ref([
    {
        searchEventId: '', //事件名
        searchTermsEventId: 'total', //筛选项
        queryType: 'and', //查询类型
        children: [
        ]
    }
])
// 筛选项列表（事件）
const searchTermsArray = ref([
    {
        value: 'total',
        label: '总次数',
    },
    {
        value: 'userNum',
        label: '用户数',
    }
])
// 大于/等于/小于/不等于
// 等于     eq
// 不等于   ne
// 大于     gt
// 小于     lt
// 区间     rang
// 空       emp
// 非空     nemp
const searchTermsArray2 = ref([
    {
        name: 'eq',
        label: '等于',
        id: '1',
    },
    {
        name: 'ne',
        label: '不等于',
        id: '2',
    },
    {
        name: 'gt',
        label: '大于',
        id: '3',
    },
    {
        name: 'lt',
        label: '小于',
        id: '4',
    },
    {
        name: 'lk',
        label: '包含',
        id: '8',
    },
    {
        name: 'nlk',
        label: '不包含',
        id: '9',
    },
    {
        name: 'rang',
        label: '区间',
        id: '5',
    },
    {
        name: 'emp',
        label: '空',
        id: '6',
    },
    {
        name: 'nemp',
        label: '非空',
        id: '7',
    }
])
// 变量名列表
const variableList = ref([])
const options = ref({
    tooltip: {
        trigger: 'axis',
        formatter: function (parames) {
            let params = parames.sort((a, b) => {
                return b.data.value - a.data.value
            })
            let str = ''
            params.forEach(item => {
                // options.value.series
                str += `
                    <div style="display:flex;align-items:center;">
                        <div style="background-color:${item.color};color:white;width:15px;height:15px;border-radius:50%;display:flex;align-items:center;justify-content: center;margin-right:5px;">
                            ${item.data?.letter}
                        </div>
                        <div>${Number(item.seriesName) ? Number(item.seriesName).toFixed(2) : (item.seriesName)}：${typeof item.data == 'number' ? item.data : item.data.value}</div>
                    </div>
                `
            })
            return `
                <div>${params[0].axisValue}</div>
                ${str}
            `;
        }
    },
    toolbox: {
        feature: {
            saveAsImage: {}
        }
    },
    xAxis: {
        type: 'category',
        data: []
    },
    yAxis: {
        type: 'value'
    },
    series: []
})
// 页面加载状态
const pageLoading = ref(true)
// dateInterval
watch(dateInterval, (newVal) => {
    if (newVal == 'hour' || newVal == 'day') {
        timeValue.value = ''
    } else {
        let sevenDays = getSevenDaysAgo();
        timeValue.value = [sevenDays[0], sevenDays[sevenDays.length - 1]]
        consecutiveDaysArray.value = getBetweenDate(sevenDays[0], sevenDays[sevenDays.length - 1])
    }
})
onBeforeMount(() => {
    operationType.value = router.currentRoute.value.query.type
    // theTest.value = true
    let sevenDays = getSevenDaysAgo();
    timeValue.value = [sevenDays[0], sevenDays[sevenDays.length - 1]]
    consecutiveDaysArray.value = getBetweenDate(sevenDays[0], sevenDays[sevenDays.length - 1])
})

// 页面加载完成
onMounted(async () => {
    pageLoading.value = false
    await getEventList();
    await getAllVariableList();
    await getAllVariableListNumber();
    colorArray.value = getRandomColor()
    if (operationType.value == 'edit') {
        let row = JSON.parse(router.currentRoute.value.query.row)
        panelName.value = row.title
        soltData.value = row.sort
        panelId.value = row.id
        panelIndex.value = router.currentRoute.value.query.index
        timeValue2.value = row.queryRange
        spliceDataToAllEventList(JSON.parse(decodeURIComponent(row.param)), row)
    }
    if (operationType.value == 'add') {
        panelIndex.value = 'add'
    }
    pageLoading.value = true
})

//hanle last page
const handleLastPage = () => {
    router.push({
        name: 'dataPanel',
        path: '/vab/statistics/dataPanel',
        params: {
            panelIndex: panelIndex.value
        }
    })
}

// 设置 事件筛选列表的值
const setEventSearchTerms = (array) => {
    let arr = []
    arr = [...array]
    arr.forEach(item => {
        item.value = item.name;
        item.label = item.remarks;
        item.children = [
            {
                value: 'sum',
                label: '总和',
            },
            {
                value: 'avg',
                label: '平均值',
            }
        ]
    })
    searchTermsArray.value = searchTermsArray.value.concat(arr)
}

// get all variable list
const getAllVariableList = async () => {
    return new Promise((resolve) => {
        api.robot.getVariableList2.get({

        })
            .then(res => {
                allVariableList.value = res.data
                resolve()
            })
    })
}

// get all variable list of Number
const getAllVariableListNumber = async () => {
    return new Promise((resolve) => {
        api.robot.getVariableList2.get({
            dataTypes: 1
        })
            .then(res => {
                setEventSearchTerms(res.data)
                resolve()
            })
    })
}


/**
 *   {
        searchEvent: '', //事件名
        searchTermsEventId: 'total', //筛选项
        children: [
            {
                searchVariable: '', //变量名
                searchTermsVariable: '', //筛选项
            }
        ]
    }
 */

// 转换 aggregateType
const aggregateTypeChange = (data, targetField) => {
    if (data == 'sum' || data == 'avg') {
        return [targetField.field, data]
    } else {
        return data
    }
}

// 固定还是动态时间
const isStaticOrDynamic = () => {
    if (dateInterval.value == 'staticDay' || dateInterval.value == 'staticHour') {
        return 'static'
    } else if (dateInterval.value == 'dynamicDay' || dateInterval.value == 'dynamicHour') {
        return 'dynamic'
    } else {
        return 'static'
    }
}


// 获取事件类型 固定时间/天/小时
const getIsHourDayMeth = (queryType, dateInterval) => {
    // 小写
    let type = queryType.toLowerCase()
    if (type == 'static') {
        if (dateInterval == 'day') {
            return 'staticDay'
        } else if (dateInterval == 'hour') {
            return 'staticHour'
        }
    } else if (type == 'dynamic') {
        if (dateInterval == 'day') {
            return 'dynamicDay'
        } else if (dateInterval == 'hour') {
            return 'dynamicHour'
        }
    }
    return ''
}

// 将 spliceData 生成的数据还原
const spliceDataToAllEventList = (data, row) => {
    let allEventData = data.basicQuery;
    allEventList.value = []
    let index = 0;

    allEventData.forEach(item => {
        let obj = {
            searchEventId: eventList.value.find(item2 => item2.name == item.event).id,
            searchTermsEventId: aggregateTypeChange(item.aggregateType, item.targetField),
            queryType: item.queryType ? item.queryType : 'and',
            children: []
        }
        item.parameter.forEach(item2 => {
            let obj2 = {
                searchVariableId: allVariableList.value.find(item3 => item3.name == item2.field).id,
                searchTermsVariableId: item2.relation,
                searchValue: item2.value
            }
            obj.children.push(obj2)
        })
        allEventList.value.push(obj)
        selectEventChange(eventList.value.find(item2 => item2.name == item.event).id, index)
        index++;
    })

    let allFilterVariableListData = data.currencyQuery;
    // 还原 allFilterVariableList 数据
    allFilterVariableList.value = []
    allFilterVariableListData.forEach(item => {
        let obj = {
            searchVariable: item.field,
            searchTermsVariable: item.relation,
            searchValue: item.value
        }
        allFilterVariableList.value.push(obj)
    })

    // 维度
    dimensionList.value = data.aggregateField.field
    // 日期
    timeValue.value = [data.timeQuery.startTime, data.timeQuery.endTime]
    // 时间区间
    consecutiveDaysArray.value = getBetweenDate(data.timeQuery.startTime, data.timeQuery.endTime)
    // 事件类型 固定时间/天/小时
    dateInterval.value = getIsHourDayMeth(row.queryType, data.timeQuery.dateInterval)
}

// watch dateInterval
watch(dateInterval, (newVal) => {
    console.error('newVal=====', newVal)
    switch (newVal) {
        case 'dynamicDay':
            timeValue2.value = timeValue2.value ? timeValue2.value : '7'
            break;
        case 'dynamicHour':
            timeValue2.value = timeValue2.value ? timeValue2.value : '24'
            break;
    }
})

const objectSpanMethod = ({ row, columnIndex }) => {
    if (columnIndex === 0) {
        if (row.data[0].classify) {
            return {
                rowspan: row.data[0].classify,
                colspan: 1
            };
        } else {
            return {
                rowspan: 0,
                colspan: 0
            };
        }
    }
};

const selectEventChange = (val, index) => {
    // 选中的事件名
    searchTermsEvent.value = ''
    // 选中的维度
    dimension.value = ''
    // 查询变量名列表
    getVariableList(val, index);
    getEventListToVariable();
}

// get the value of eventList
const getEventList = () => {
    return new Promise((resolve, reject) => {
        api.robot.getEventList.get({}).then(res => {
            if (res.code === 200) {
                eventList.value = res.data
                resolve()
            } else {
                ElMessage.error(res.msg)
                reject()
            }
        })
    })
}

// get the value of variableList
const getVariableList = (val, index) => {
    api.robot.getVariableList3.get({
        // parentId: 0,
        eventIds: val
    }).then(res => {
        if (res.code === 200) {
            variableList.value = res.data
            allEventList.value[index].variableList = res.data
            if (!variableList.value.length) {
                ElMessage.error('该事件不包含变量')
            }
        } else {
            ElMessage.error(res.msg)
        }
    })
}

// handle add event
const handleAddEvent = (bool, index) => {
    if (bool) {
        allEventList.value.push({
            searchEventId: '', //事件名
            searchTermsEventId: 'total', //筛选项
            queryType: 'and',
            children: [

            ]
        })
        getEventListToVariable()
    } else {
        ElMessageBox.confirm('此操作将删除该事件, 是否继续?', '提示', {
            confirmButtonText: '确定',
            cancelButtonText: '取消',
            type: 'warning'
        })
            .then(() => {
                // delete event
                allEventList.value.splice(index, 1)
                getEventListToVariable()
                ElMessage.success('删除成功')
            })
            .catch(() => {
                ElMessage.info('已取消删除')
            })
    }
}

// hanle add filter
const handleAddFilter = (bool, index) => {
    if (bool) {
        allFilterVariableList.value.push({
            searchVariableId: '', //变量名
            searchTermsVariableId: '', //筛选项
        })
    } else {
        ElMessageBox.confirm('此操作将删除该变量, 是否继续?', '提示', {
            confirmButtonText: '确定',
            cancelButtonText: '取消',
            type: 'warning'
        })
            .then(() => {
                // delete variable
                allFilterVariableList.value.splice(index, 1)
                ElMessage.success('删除成功')
            })
            .catch(() => {
                ElMessage.info('已取消删除')
            })
    }
}

//hanle add variable
const handleAddVariable = (bool, index, indexes) => {
    if (bool) {
        allEventList.value[index].children.push({
            searchVariableId: '', //变量名
            searchTermsVariableId: '', //筛选项
        })
    } else {
        ElMessageBox.confirm('此操作将删除该变量, 是否继续?', '提示', {
            confirmButtonText: '确定',
            cancelButtonText: '取消',
            type: 'warning'
        })
            .then(() => {
                // delete variable
                allEventList.value[index].children.splice(indexes, 1)
                ElMessage.success('删除成功')
            })
            .catch(() => {
                ElMessage.info('已取消删除')
            })
    }
}

// 查询所有事件对应的变量名
const getEventListToVariable = () => {
    let parentIdList = []
    allEventList.value.forEach(item => {
        if (item.searchEventId) {
            parentIdList.push(item.searchEventId)
        }
    })
    api.robot.getVariableList3.get({
        // parentId: 0,
        eventIds: parentIdList.join(',')
    }).then(res => {
        if (res.code === 200) {
            eventVariableList.value = res.data
        } else {
            ElMessage.error(res.msg)
        }
    })
}

// 整理 searchTermsEventId 的值，提供查询
const getSearchTermsEventId = (val) => {
    let type = typeof val;
    if (type == 'string') {
        return val
    } else {
        let len = val.length;
        if (len == 1) {
            return val[0]
        } else {
            let obj = allVariableList.value.find(item => item.name === val[0])
            return {
                msg: val[1],
                "targetField": {
                    "field": obj.name,
                    "propertiesType": obj.propertiesType,
                    "dataType": obj.dataType,
                },
            }
        }
    }
}

// splice data
const spliceData = (allEvent) => {
    let data = {
        "basicQuery": [],
        "queryType": "",
        "currencyQuery": [],
        "timeQuery": {
            dateInterval: "",
            "startTime": "",
            "endTime": ""
        },
        "aggregateField": ""
    }
    let bool = true
    allEvent.forEach(item => {
        let param = []
        item.children.forEach(i => {
            if (i.searchVariableId) {
                param.push({
                    field: item.variableList.filter(e => e.id === i.searchVariableId)[0].name,
                    relation: i.searchTermsVariableId,
                    value: i.searchValue,
                    dataType: item.variableList.filter(e => e.id === i.searchVariableId)[0].dataType,
                    propertiesType: item.variableList.filter(e => e.id === i.searchVariableId)[0].propertiesType
                })
            }
        })
        if (item.searchEventId) {
            let searchTermsEventIdCopy = getSearchTermsEventId(item.searchTermsEventId);
            let obj = {
                aggregateType: "",
                event: eventList.value.filter(i => i.id === item.searchEventId)[0].name,
                queryType: item.queryType,
                parameter: param
            }
            if (typeof searchTermsEventIdCopy == 'string') {
                obj.aggregateType = searchTermsEventIdCopy;
            } else {
                obj.aggregateType = searchTermsEventIdCopy.msg;
                obj.targetField = searchTermsEventIdCopy.targetField;
            }
            data.basicQuery.push(obj)
        }
    })
    allFilterVariableList.value.forEach(item => {
        let variable = eventVariableList.value.filter(i => i.name === item.searchVariable)
        data.currencyQuery.push({
            field: item.searchVariable,
            dataType: item.searchVariable && eventVariableList.value.filter(i => i.name === item.searchVariable)[0].dataType,
            relation: item.searchTermsVariable,
            value: item.searchValue,
            propertiesType: variable.length ? variable[0].propertiesType : ''
        })
    })
    let timeValueArray = [];
    // getFewDaysAgo
    // 判断时间类型 固定时间/天/日

    if (dateInterval.value === 'dynamicDay') {
        if (timeValue2.value) {
            let timeArray = getFewDaysAgo(timeValue2.value)
            timeValueArray = [timeArray[0], timeArray[timeArray.length - 1]]
            timeValue.value = timeValueArray
            consecutiveDaysArray.value = getBetweenDate(timeValueArray[0], timeValueArray[1])
        } else {
            bool = false
            ElMessage.error('请选择时间')
        }
    } else if (dateInterval.value === 'dynamicHour') {
        if (timeValue2.value) {
            let timeArray = getFewHourAgo(timeValue2.value)
            timeValueArray = [timeArray[0], timeArray[timeArray.length - 1]]
            timeValue.value = timeValueArray
            consecutiveDaysArray.value = getBetweenHour(timeValueArray[0], timeValueArray[1])
        } else {
            bool = false
            ElMessage.error('请选择时间')
        }
    } else if (dateInterval.value === 'staticHour') {
        consecutiveDaysArray.value = getBetweenHourNum(timeValue.value[0], timeValue.value[timeValue.value.length - 1])
        timeValue.value = [consecutiveDaysArray.value[0], consecutiveDaysArray.value[consecutiveDaysArray.value.length - 1]]
    } else {
        timeValueArray = timeValue.value
    }

    data.timeQuery.startTime = timeValue.value.length ? timeValue.value[0] : ''
    if (dateInterval.value != 'dynamicHour' && dateInterval.value != 'staticHour') {

        data.timeQuery.endTime = timeValue.value.length ? timeValue.value[1].split(' ')[0] + ' 23:59:59' : ''
    } else {
        data.timeQuery.endTime = timeValue.value.length ? timeValue.value[1] : ''
    }

    data.timeQuery.dateInterval = dateInterval.value === 'staticHour' || dateInterval.value === 'dynamicHour' ? 'hour' : 'day'
    data.aggregateField = {
        dataType: dimensionList.value ? (eventVariableList.value.filter(i => i.name === dimensionList.value)[0].dataType) : '',
        field: dimensionList.value,
        aggregateType: dimensionList.value ? (eventVariableList.value.filter(i => i.name === dimensionList.value)[0].propertiesType) : ''
    }

    return bool ? data : null
}

// get fileter value
const getFilterValue = (value) => {
    return new Promise((resolve) => {
        api.robot.getFilterValue.get({
            data: encodeURIComponent(JSON.stringify(value)),
        }).then(res => {
            if (res.code === 200) {
                resolve(res.data)
            } else {
                ElMessage.error(res.msg)
            }
        })
    })
}

// Get the date of today and seven days ago
const getSevenDaysAgo = () => {
    let dateArr = []
    let date = new Date()
    let time = date.getTime()
    for (let i = 0; i < 7; i++) {
        dateArr.unshift(new Date(time - 1000 * 60 * 60 * 24 * i).toLocaleDateString())
    }
    // 转换日期格式
    dateArr = dateArr.map(item => {
        let arr = item.split('/')
        return arr[0] + '-' + (Number(arr[1]) < 10 ? '0' + arr[1] : arr[1]) + '-' + (Number(arr[2]) < 10 ? '0' + arr[2] : arr[2]) + ' 00:00:00'
    })
    return dateArr
}

// Get the date of today and the specified few days ago
const getFewDaysAgo = (num) => {
    let dateArr = []
    let date = new Date()
    let time = date.getTime()
    for (let i = 0; i < num; i++) {
        dateArr.unshift(new Date(time - 1000 * 60 * 60 * 24 * i).toLocaleDateString())
    }
    // 转换日期格式
    dateArr = dateArr.map(item => {
        let arr = item.split('/')
        return arr[0] + '-' + (Number(arr[1]) < 10 ? '0' + arr[1] : arr[1]) + '-' + (Number(arr[2]) < 10 ? '0' + arr[2] : arr[2]) + ' 00:00:00'
    })
    return dateArr
}
// Get the date of today and the specified few hour ago and year month day
const getFewHourAgo = (num) => {
    let dateArr = []
    let date = new Date()
    let time = date.getTime()
    for (let i = 0; i < num; i++) {
        dateArr.unshift(new Date(time - 1000 * 60 * 60 * i))
    }

    // 转换日期格式
    dateArr = dateArr.map(item => {
        // 获取当前年月日
        let year = item.getFullYear()
        let month = item.getMonth() + 1
        let day = item.getDate()
        let arr = item.toLocaleTimeString().split(':')
        return year + '-' + (month < 10 ? '0' + month : month) + '-' + (day < 10 ? '0' + day : day) + ' ' + arr[0] + ':' + '00' + ':00'
    })

    dateArr[dateArr.length - 1] = dateArr[dateArr.length - 1].split(' ')[0] + ' ' + dateArr[dateArr.length - 1].split(' ')[1].split(':')[0] + ':59:59'
    return dateArr
}

// 随机生成20种颜色
const getRandomColor = () => {
    let color = ['#488BFF', '#26CEBA', '#FFC069', '#FD6865', '#6D7EE4', '#FF9C6E', '#81C785', '#B47FEC', '#62A5F6', '#FF85C0', '#CADEA9', '#EFBA52', '#DFB4CF', '#E99897', '#C5E3E3', '#6E2761', '#D09C6D', '#78C1CA', '#D4DE87', '#CCDDEF']
    // for (let i = 0; i < 20; i++) {
    //     color.push('#' + Math.floor(Math.random() * 0xffffff).toString(16).padEnd(6, '0'))
    // }
    return color
}

// get the date bettween start time and end time
const getBetweenDate = (start, end) => {
    let dateArr = []
    let startTime = new Date(start)
    let endTime = new Date(end)
    while ((endTime.getTime() - startTime.getTime()) >= 0) {
        let year = startTime.getFullYear()
        let month = (startTime.getMonth() + 1) < 10 ? '0' + (startTime.getMonth() + 1) : (startTime.getMonth() + 1)
        let day = startTime.getDate() < 10 ? '0' + startTime.getDate() : startTime.getDate()
        dateArr.push(year + '-' + month + '-' + day)
        startTime.setDate(startTime.getDate() + 1)
    }
    return dateArr
}
// Get hours between start time and end time
const getBetweenHour = (start, end) => {
    let dateArr = []
    let startTime = new Date(start)
    let endTime = new Date(end)
    while ((endTime.getTime() - startTime.getTime()) >= 0) {
        let year = startTime.getFullYear()
        let month = (startTime.getMonth() + 1) < 10 ? '0' + (startTime.getMonth() + 1) : (startTime.getMonth() + 1)
        let day = startTime.getDate() < 10 ? '0' + startTime.getDate() : startTime.getDate()
        let hour = startTime.getHours() < 10 ? '0' + startTime.getHours() : startTime.getHours()
        let minute = startTime.getMinutes() < 10 ? '0' + startTime.getMinutes() : startTime.getMinutes()
        dateArr.push(year + '-' + month + '-' + day + ' ' + hour + ':' + minute + ':00')
        startTime.setHours(startTime.getHours() + 1)
    }
    dateArr[dateArr.length - 1] = dateArr[dateArr.length - 1].split(' ')[0] + ' ' + dateArr[dateArr.length - 1].split(' ')[1].split(':')[0] + ':00:00'
    return dateArr
}

// 获取日期之间的小时数
const getBetweenHourNum = (start, end) => {
    start = start.split(' ')[0] + ' 00:00:00'
    end = end.split(' ')[0] + ' 23:59:59'
    let dateArr = []
    let startTime = new Date(start)
    let endTime = new Date(end)
    while ((endTime.getTime() - startTime.getTime()) >= 0) {
        let year = startTime.getFullYear()
        let month = (startTime.getMonth() + 1) < 10 ? '0' + (startTime.getMonth() + 1) : (startTime.getMonth() + 1)
        let day = startTime.getDate() < 10 ? '0' + startTime.getDate() : startTime.getDate()
        let hour = startTime.getHours() < 10 ? '0' + startTime.getHours() : startTime.getHours()
        let minute = startTime.getMinutes() < 10 ? '0' + startTime.getMinutes() : startTime.getMinutes()
        dateArr.push(year + '-' + month + '-' + day + ' ' + hour + ':' + minute + ':00')
        startTime.setHours(startTime.getHours() + 1)
    }
    dateArr[dateArr.length - 1] = dateArr[dateArr.length - 1].split(' ')[0] + ' ' + dateArr[dateArr.length - 1].split(' ')[1].split(':')[0] + ':59:59'
    return dateArr
}

const datePickChange = (value) => {
    consecutiveDaysArray.value = getBetweenDate(value[0], value[1])
}

// 按照echarts的数据格式，将后台返回的数据转换成echarts的数据格式
const formatEchartsData = (data) => {
    let keyArr = Object.keys(data);
    let allArr = [];
    eventColorObj.value = [];
    let colorIndex = 0;
    let letterIndex = 0;

    keyArr.forEach(item => {
        let chinese = eventList.value.find(e => e.name === item.split('.')[0])?.remarks
        let arr = []
        let obj = {
            eventName: item,
            variableArray: [],
            chinese
        }
        for (let i = 0; i < consecutiveDaysArray.value.length; i++) {
            if (data[item][changeLastTime(consecutiveDaysArray.value[i])]) {
                data[item][changeLastTime(consecutiveDaysArray.value[i])].forEach(e => {
                    if (arr.find(i => i.name === e.agg)) {
                        arr.find(i => i.name === e.agg).data[i] = {
                            value: e.count,
                            letter: letterArray.value[letterIndex],
                            tab: consecutiveDaysArray.value[i],
                            event: item,
                            chinese
                        }
                    } else {
                        let arr1 = new Array(consecutiveDaysArray.value.length).fill({
                            value: 0,
                            letter: letterArray.value[letterIndex],
                            tab: '',
                            event: item,
                            chinese
                        });
                        arr1[i] = {
                            value: e.count,
                            letter: letterArray.value[letterIndex],
                            tab: consecutiveDaysArray.value[i],
                            event: item,
                            chinese
                        }
                        arr.push({
                            name: e.agg,
                            data: arr1,
                            type: 'line',
                            itemStyle: {
                                normal: {
                                    color: colorArray.value[colorIndex],
                                    lineStyle: {
                                        color: colorArray.value[colorIndex]
                                    }
                                }
                            },
                        })
                        obj.variableArray.push({
                            variableName: e.agg,
                            color: colorArray.value[colorIndex],
                        })
                        colorIndex++

                    }

                })
            } else {
                data[item][consecutiveDaysArray.value[i]] = []
            }
        }
        letterIndex++
        allArr.push(arr)
        eventColorObj.value.push(obj);
    })

    allArr = allArr.flat()

    let nameArr = []
    for (let i = 0; i < allArr.length; i++) {
        nameArr.push(allArr[i].name)
    }

    return {
        allArr,
        nameArr
    }
}

// 获取数组前20位
const getTop20 = (arr) => {
    let newArr = []
    if (arr.length >= 20) {
        // tip
        ElMessage({
            message: '折线图最多只能显示20条数据，其余数据请查表格！！！',
            type: 'warning'
        })
        for (let i = 0; i < 20; i++) {
            newArr.push(arr[i])
        }
    } else {
        newArr = arr
    }
    return newArr
}

// 深拷贝
const deepClone = (obj) => {
    let _obj = JSON.stringify(obj),
        objClone = JSON.parse(_obj);
    return objClone
}

// 填充日期和分类
const fillDateAndClass = (arr) => {
    let index = 0;
    let data = deepClone(arr)
    let startIndex = 0;
    let oldIndex = 0;
    let classify = '';
    while (startIndex < data.length) {
        if (data[startIndex].data[0].letter == classify) {
            startIndex++;
        } else {
            classify = data[startIndex].data[0].letter;
            data[oldIndex].data[0].classify = startIndex - oldIndex;
            oldIndex = startIndex;
        }
        if (startIndex == data.length - 1) {
            data[oldIndex].data[0].classify = startIndex - oldIndex + 1;
        }
    }
    consecutiveDaysArray.value.forEach(item => {
        data.forEach(e => {
            e.data[index].tab = item //填充日期
        })
        index++
    })
    return data
}

const handleAddPanel = () => {
    api.robot.addPanel.post({
        "title": panelName.value || '默认面板名称',
        "param": encodeURIComponent(JSON.stringify(spliceData(allEventList.value))),
        "userId": 1,
        "sort": soltData.value || 1,
        "queryRange": timeValue2.value,
        'queryType': isStaticOrDynamic()
    })
        .then(res => {
            if (res.code == 200 && res.data == true) {
                ElMessage({
                    message: '添加成功',
                    type: 'success'
                })
                // 跳转页面post
                // router.push({
                //     name: 'dataPanel',
                //     path: '/vab/statistics/dataPanel',
                //     params: {
                //         panelIndex: panelIndex.value
                //     }
                // })
                handleLastPage();
            } else {
                ElMessage({
                    message: '添加失败',
                    type: 'error'
                })
            }
        })
}

const handleEditPanel = () => {
    api.robot.editPanel.put({
        "id": panelId.value,
        "title": panelName.value || '默认面板名称',
        "param": encodeURIComponent(JSON.stringify(spliceData(allEventList.value))),
        "userId": 1,
        "sort": soltData.value || 1,
        "queryRange": timeValue2.value,
        'queryType': isStaticOrDynamic()
    })
        .then(res => {
            if (res.code == 200 && res.data == true) {
                ElMessage({
                    message: '修改成功',
                    type: 'success'
                })
            } else {
                ElMessage({
                    message: '修改失败',
                    type: 'error'
                })
            }
        })
}

// 排序求总和
const sortAndSum = (arr) => {
    arr.forEach(item => {
        let sum = 0;
        item.data.forEach(e => {
            sum += e.value
        })
        item.sum = sum
    })
    let arr2 = [];
    let p = "";
    arr.forEach(item => {
        if (item.data[0].letter != p) {
            arr2.push([])
            arr2[arr2.length - 1].push(item)
            p = item.data[0].letter
        } else {
            arr2[arr2.length - 1].push(item)
        }
    })
    // 排序
    arr2.forEach(item => {
        let classify = item[0].data[0].classify
        item[0].data[0].classify = "";
        item.sort((a, b) => {
            return b.sum - a.sum
        })
        item[0].data[0].classify = classify;
    })

    // arr.sort((a, b) => {
    //     return b.sum - a.sum
    // })
    let arr3 = arr2.flat()
    return arr3
}

// 将最后一项改成 00:00
const changeLastTime = (str) => {
    if (dateInterval.value == 'hour') {
        let newStr = '';
        let ymd = str.split(' ')[0]
        let hms = str.split(' ')[1].split(':')[0]
        newStr = `${ymd} ${hms}:00:00`
        return newStr
    } else {
        return str
    }
}

// 时间戳转换成秒
const timeToS = (startTime, endTime) => {
    if (endTime - startTime < 1000) {
        return endTime - startTime + ' 毫秒'
    }

    let s = (endTime - startTime) / 1000;
    return s + ' 秒'
}

//render options
const renderOptions = () => {
    if (!pageLoading.value) {
        //tip
        ElMessage({
            message: '数据加载中，请稍后...',
            type: 'warning'
        })
        return
    }
    loading.value = true
    let time = new Date().getTime();
    let meeageObj = ElMessage({
        message: '数据查询中，请稍后...',
        type: 'warning',
        icon: 'el-icon-loading',
        duration: 0
    })

    echartsRef.value.clear()
    let filterObj2 = spliceData(allEventList.value)
    let filterObj = {}
    if(eventType.value=='right'){
        Object.keys(filterObj2).forEach(item=>{
            if(item=='basicQuery'){
                filterObj['customQuery'] = filterObj2[item]
            }else{
                filterObj[item] = filterObj2[item]
            }
        })
    }else{
        filterObj = filterObj2
    }
    if (!filterObj) {
        loading.value = false
        return
    }
    let time1 = 0;
    getFilterValue(filterObj)
        .then(res => {
            time1 = new Date().getTime();
            let { allArr } = formatEchartsData(res)
            let allArr2 = fillDateAndClass(allArr)
            options.value.xAxis.data = consecutiveDaysArray.value
            options.value.series = []
            // options.value.series = getTop20(allArr2);
            seriesObj.value = sortAndSum(deepClone(allArr2))
            options.value.series = getTop20(seriesObj.value);
        })
        .catch(() => {
            loading.value = false
            time1 = new Date().getTime();
        })
        .finally(() => {
            loading.value = false
            // close ElMessage
            meeageObj.close()
            ElMessage({
                message: '数据加载完成！共用时 ' + timeToS(time, time1) + '！',
                type: 'success',
                duration: 3000
            })
        })
}
</script>
<style scoped>
.topclass {
    margin: 20px;
    background-color: white;
    padding: 20px;
}

.spanclass {
    margin: 0 10px;
}

.shaiclass {
    display: inline-block;
    margin-left: 10px;
}

.topclassChild {
    margin-top: 0;
    margin-bottom: 0;
    padding-bottom: 0;
    margin-left: 0;
    padding-left: 0;
}

.colorObj {
    box-shadow: 0 0 5px #e9e9eb;
    padding: 5px 20px;
    border-radius: 10px;
}

.yuan {
    width: 10px;
    height: 10px;
    border-radius: 50%;
    margin-right: 10px;
}

.disp {
    display: flex;
    align-items: center;
    font-weight: 500;
    margin-left: 20px;
}

.letterclass {
    width: 15px;
    height: 15px;
    border-radius: 50%;
    display: flex;
    align-items: center;
    justify-content: center;
    color: white;
    background-color: #409EFF;
    margin-right: 3px;
}

.bianlinagall {
    display: flex;
}

.bianlinagLeft {
    width: 40px;
    min-height: 100%;
    display: flex;
    align-items: center;
    margin-left: 30px;
}

.hehuoall {
    border: 1px solid #409EFF;
    border-radius: 4px;
    margin-top: 17px;
}

.hehuo {
    font-size: 13px;
    padding: 6px;
    cursor: pointer;
}
</style>
