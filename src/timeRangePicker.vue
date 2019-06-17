<template>
    <div class="xg-time-range" v-if="localShow">
        <div class="xg-header">
            <div class="xg-nav">
                <button @click="toggle">取消</button>
                选择日期            
            </div>
            <div class="xg-week">
                <span>日</span>
                <span>一</span>
                <span>二</span>
                <span>三</span>
                <span>四</span>
                <span>五</span>
                <span>六</span>
            </div>
        </div>
        <div class="xg-main">
            <div class="xg-container">
                <div class="xg-warper">
                    <div class="xg-month" v-for="(month, index) in days" :key="index">
                        <div class="xg-year-month">{{month.yearMonth}}</div>
                        <div class="xg-days">
                            <div v-for="(item, i) in month.list" :class="classNames(item)" :key="i" @click="select(item, $event)">
                                <strong class="xg-tips">{{getTips(item)}}</strong>
                                <span>{{item.day}}</span>
                                <em class="xg-holiday"></em>
                            </div>
                        </div>
                    </div>                
                </div>
            </div>
        </div>
    </div>
</template>

<script>
const monthDay = [31, 28, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31]
export default {
    name: 'timeRangePicker',
    data () {
        let {maxDay} = this
        if (maxDay > 300) {
            console.error('最大不能超过300天，默认90天')
            maxDay = 90
        }
        const currentDate = new Date()
        const currentYear = currentDate.getFullYear()
        const currentDateMonth = currentDate.getMonth()
        const currentDateDate = currentDate.getDate()
        const lastDate = new Date(Date.now() + maxDay * 1000 * 60 * 60 * 24)
        const lastDateMonth = lastDate.getMonth()
        const lastDateYear = lastDate.getFullYear()
        const lastDateDate = lastDate.getDate()
        let _currentDateMonth = currentDateMonth < 9 ? ('0' + (currentDateMonth + 1)) : (currentDateMonth + 1)
        let _currentDateDate = currentDateDate < 10 ? ('0' + currentDateDate) : currentDateDate
        let _lastDateMonth = lastDateMonth < 9 ? ('0' + (lastDateMonth + 1)) : (lastDateMonth + 1)
        let _lastDateDate = lastDateDate < 10 ? ('0' + lastDateDate) : lastDateDate
        const {dateRange} = this
        let start = '', end = ''
        if (dateRange.length) {
            start = dateRange[0]
            end = dateRange[1]
        }
        return {
            alldays: [],
            days: [],
            start,
            end,
            currentYear,
            currentDateMonth,
            currentDateDate,
            lastDateYear,
            lastDateMonth,
            lastDateDate,
            lastDay: `${lastDateYear}-${_lastDateMonth}-${_lastDateDate}`,
            today: `${currentYear}-${_currentDateMonth}-${_currentDateDate}`
        }
    },
    methods: {
        toggle () {
            this.localShow = !this.localShow
        },
        select (item) {
            const {start, end, today, lastDay} = this
            if (!item) {
                return false
            }
            const {date} = item
            if (date < today || date > lastDay) {
                return false
            }
            if (start) {
                if (start === date) {
                    return false
                }
                if (date < this.start || end) {
                    this.start = date
                    this.end = ''
                    return false
                }
                this.end = date
                this.$emit('change', {start, end: date, list: [start, ...this.range, date]})
            } else {
                this.start = date
            }
        },
        getTips ({date}) {
            const {start, end, startText, endText} = this
            return date === start ? startText : date === end ? endText : ''
        },
        classNames ({date}) {
            const {start, end, today, lastDay, range} = this
            return {
                'over': date && (date < today || date > lastDay),
                'actived': (date && (date === start || date === end)) ? true : false,
                'start': date === start,
                'end': date === end,
                'range': range.includes(date),
                'null': !date
            }
        }
    },
    computed: {
        localShow: {
            get () {
                return this.value
            },
            set (value) {
                this.$emit('input', value)
            }
        },
        range () {
            const {start, end, alldays, isSelectedClose} = this
            if (!end) {
                return []
            }
            if (isSelectedClose) {
                this.localShow = false
            }
            return alldays.filter(v => {
                return (v > start && v < end)
            })
        }
    },
    props: {
        isSelectedClose: {
            type: Boolean,
            default: false
        },
        maxDay: {
            type: Number,
            default: 90
        },
        dateRange: {
            type: Array,
            default: () => []
        },
        value: {
            type: Boolean,
            default: false
        },
        startText: {
            type: String,
            default: '开始'
        },
        endText: {
            type: String,
            default: '结束'
        }
    },
    watch: {
        dateRange (v) {
            if (v.length) {
                this.start = v[0]
                this.end = v[1]
            }
        }
    },
    created () {
        const days = []
        const alldays = []
        // const {maxDay = 90} = this.props
        const {currentDateMonth, lastDateMonth, currentYear} = this
        let k = currentDateMonth
        let i = 0
        while (k !== (lastDateMonth + 1)) {
            let o = 1
            let m = currentYear
            if (m % 4 === 0) {
                monthDay[1] = 29
            } else {
                monthDay[1] = 28
            }
            const day = new Date(`${m}-${k+1}-${o}`).getDay()
            days.push({
                yearMonth: m + '-' + (k + 1),
                list: new Array(day).fill(0)
            })
            while(o <= monthDay[k]) {
                let _k = k+1
                _k = _k < 10 ? ('0' + _k) : _k 
                const _date = `${m}-${_k}-${o < 10 ? ('0' + o) : o}`
                days[i].list.push({
                    day: o,
                    date: _date
                })
                alldays.push(_date)
                o++
            }
            i++
            k++
            if (k === 12) {
                k = 0
            }
        }
        this.days = Object.freeze(days)
        this.alldays = Object.freeze(alldays)
    },
}
</script>

<style scoped lang="less">
    .xg-time-range {
        background-color: #fff;
        position: fixed;
        top: 0;
        left: 0;
        width: 100%;
        height: 100%;
        height: 100vh;
        overflow: hidden;
        .xg-header {
            position: absolute;
            width: 100%;
            left: 0;
            top: 0;
            height: 75px;
            z-index: 2;
            .xg-nav {
                text-align: center;
                height: 45px;
                line-height: 45px;
                font-weight: bold;
                position: relative;
                button {
                    background-color: transparent;
                    position: absolute;
                    left: 15px;
                    top: 5px;
                    line-height: 35px;
                    display: table;
                    height: 35px;
                    outline: none;
                    border: 0;
                }
                &:after {
                    position: absolute;
                    left: 0;
                    bottom: 0;
                    width: 100%;
                    display: block;
                    content: ' ';
                    border-bottom: 1px solid #ccc;
                }                
            }
            .xg-week {
                display: table;
                width: 100%;
                height: 30px;
                position: relative;
                &:after {
                    position: absolute;
                    left: 0;
                    bottom: 0;
                    width: 100%;
                    display: block;
                    content: ' ';
                    border-bottom: 1px solid #eee;
                }
                span {
                    vertical-align: middle;
                    display: table-cell;
                    text-align: center;
                }
            }
        }
        .xg-main {
            height: 100%;
            padding-top: 75px;
            overflow: hidden;
            box-sizing: border-box;
            position: relative;
            .xg-container {
                height: 100%;
                overflow: auto;
            }
            .xg-year-month {
                text-align: center;
                height: 30px;
                line-height: 30px;
                background-color: rgb(113, 113, 253);
                color: #fff;
            }
            .xg-days {
                &::after {
                    content: ' ';
                    clear: both;
                    display: block;
                }
                div {
                    float: left;
                    text-align: center;
                    width: 14.285714%;
                    height: 75px;
                    overflow: hidden;
                    &.actived {
                        background-color: rgb(157, 157, 255) !important;
                        // color: #fff;
                    }
                    &:nth-of-type(7n+1), &:nth-of-type(7n+7) {
                        background-color: #fff7f3;
                    }
                    &.null {
                        background-color: #fff;
                    }
                    &.over {
                        background-color: rgb(236, 234, 234);
                        color: #999;
                    }
                    &.range {
                        background-color: rgb(194, 194, 255) !important;
                    }
                    border-bottom: 1px solid #e8e8e8;
                    strong, em {
                        display: block;
                        text-align: center;
                        width: 100%;
                        font-style: normal;
                        font-weight: lighter;
                    }
                    .xg-holiday {
                        height: 20px;
                        font-size: 12px;
                    }
                    .xg-tips {
                        color: #fff;
                        margin-top: 10px;
                        height: 20px;
                        font-size: 14px;
                    }
                    span {
                        width: 100%;
                        display: block;
                        height: 25px;
                        text-align: center;
                        font-weight: bold;
                    }
                }
            }
        }
    }
</style>

