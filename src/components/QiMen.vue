<template>
    <el-form size="mini" :model="qCalcForm">
        <div class="qCalc">
            <div v-for="s in 9" :class="['square', 's' + s]">
                <template v-if="s !== 5">
                    <div class="cell c1">
                        <p>{{ getSquareScore(s) }}</p>
                    </div>
                    <div class="cell c2">
                        <el-form-item>
                            <el-select v-model="qCalcForm[`s${s}_shenZhu`]" clearable>
                                <el-option v-for="(value, key) in shenZhuMap" :key="key" :label="key" :value="key">
                                    <span style="float: left" :class="{ red: value > 0 }">{{ key }}</span>
                                    <span style="float: right">{{
                                        value
                                    }}</span>
                                </el-option>
                            </el-select>
                        </el-form-item>
                    </div>
                    <div class="cell c3"></div>
                    <div class="cell c4"></div>
                    <div class="cell c5">
                        <el-form-item>
                            <el-select v-model="qCalcForm[`s${s}_tianShi`]" clearable>
                                <el-option v-for="(value, key) in tianShiMap" :key="key" :label="key" :value="key">
                                    <span style="float: left" :class="{ red: value > 0 }">{{ key }}</span>
                                    <span style="float: right">{{
                                        value
                                    }}</span>
                                </el-option>
                            </el-select>
                        </el-form-item>
                        <span class="square-type">{{
                            squareTypeMap["s" + s]
                        }}</span>
                    </div>
                    <div class="cell c6">
                        <el-form-item>
                            <el-cascader v-model="qCalcForm[`s${s}_zs12`]" :options="zs12Casc"
                                :props="{ checkStrictly: true }" clearable>
                                <template slot-scope="{ node, data }">
                                    <span>{{ data.label }}</span>
                                    <span :class="{
                                        red: zs12Map[data.label] > 0,
                                    }">&nbsp;&nbsp;{{
                                        zs12Map[data.label]
                                    }}</span>
                                </template>
                            </el-cascader>
                        </el-form-item>
                        <el-form-item>
                            <el-select v-model="qCalcForm[`s${s}_sky`]" clearable>
                                <el-option v-for="(value, key) in tianganMap" :key="key" :label="key" :value="key">
                                    <span style="float: left">{{ key }}</span>
                                    <span style="float: right">{{
                                        value
                                    }}</span>
                                </el-option>
                            </el-select>
                        </el-form-item>
                    </div>
                    <div class="cell c7"></div>
                    <div class="cell c8">
                        <el-form-item>
                            <el-select v-model="qCalcForm[`s${s}_renHe`]" clearable>
                                <el-option v-for="(value, key) in renHeMap" :key="key" :label="key" :value="key">
                                    <span style="float: left" :class="{ red: value > 0 }">{{ key }}</span>
                                    <span style="float: right">{{
                                        value
                                    }}</span>
                                </el-option>
                            </el-select>
                        </el-form-item>
                    </div>
                    <div class="cell c9">
                        <el-form-item>
                            <el-select v-model="qCalcForm[`s${s}_earth`]" clearable>
                                <el-option v-for="(value, key) in tianganMap" :key="key" :label="key" :value="key">
                                    <span style="float: left">{{ key }}</span>
                                    <span style="float: right">{{
                                        value
                                    }}</span>
                                </el-option>
                            </el-select>
                        </el-form-item>
                    </div>
                </template>
            </div>
        </div>
    </el-form>
</template>

<script>
export default {
    data() {
        return {
            qCalcForm: {},
            squareScoreMap: {},
            // 神助计分
            shenZhuMap: {
                值符: 20,
                太阴: 20,
                六合: 20,
                九天: 20,
                腾蛇: 0,
                白虎: 0,
                玄武: 0,
                九地: 0,
            },
            // 天时计分
            tianShiMap: {
                天任星: 20,
                天冲星: 20,
                天辅星: 20,
                天心星: 20,
                天禽星: 20,
                天蓬星: 0,
                天英星: 0,
                天芮星: 0,
                天柱星: 0,
            },
            // 人和计分
            renHeMap: {
                休门: 30,
                生门: 30,
                开门: 30,
                杜门: 0,
                景门: 0,
                死门: 0,
                惊门: 0,
                伤门: 0,
            },
            tianganMap: {
                甲: "阳木",
                乙: "阴木",
                丙: "阳火",
                丁: "阴火",
                戊: "阳土",
                己: "阴土",
                庚: "阳金",
                辛: "阴金",
                壬: "阳水",
                癸: "阴水",
            },
            // 十二长生周期计分
            zs12Map: {
                长: 30,
                沐: 30,
                冠: 30,
                临: 30,
                旺: 30,
                衰: 20,
                病: 20,
                死: 20,
                墓: 0,
                绝: 10,
                胎: 10,
                养: 10,
            },
            cellCalcMap: {
                c2: "shenZhuMap",
                c5: "tianShiMap",
                c6: "zs12Map",
                c8: "renHeMap",
            },
            wuxing: ["木", "火", "土", "金", "水"],
            // 九宫格五行属性
            squareTypeMap: {
                s1: "木",
                s2: "火",
                s3: "土",
                s4: "木",
                s5: "土",
                s6: "金",
                s7: "土",
                s8: "水",
                s9: "金",
            },
        }
    },
    computed: {
        // 12周期级联选择
        zs12Casc() {
            let arr = Object.keys(this.zs12Map)
            return arr.map((val, index) => {
                let children = [...arr.slice(0, index), ...arr.slice(index + 1)] // 除去本身
                children = children.map((item) => {
                    return {
                        label: item,
                        value: item,
                    }
                })
                let obj = {
                    label: val,
                    value: val,
                    children,
                }
                return obj
            })
        },
    },
    watch: {
        qCalcForm: {
            handler(val) {
                let obj = {}
                for (let key in val) {
                    let arr = key.split("_")
                    let s = arr[0]
                    let c = arr[1]
                    let square = obj[s]
                    if (!square) {
                        obj[s] = {}
                        square = obj[s]
                    }
                    square[c] = val[key]
                }
                console.log(obj)
                this.squareScoreMap = obj
            },
            deep: true,
        },
    },
    mounted() { },
    methods: {
        /**
         * 计算宫格分数
         * @param s 宫格序号
         */
        getSquareScore(s) {
            let square = this.squareScoreMap["s" + s]
            let sum = 200
            let trsz = ["tianShi", "renHe", "shenZhu"] // 天时 人和 神助直接计分
            for (let c in square) {
                let value = square[c]
                let num = 0
                if (value && value.length) {
                    if (trsz.includes(c) && value) {
                        num = this[`${c}Map`][value]
                    }
                    if (c === "zs12" && value.length) {
                        let temp = this.zs12Map[value[0]]
                        let last = value[1]
                        if (last) {
                            temp += this.zs12Map[last]
                        }
                        num = temp / value.length // 取平均值
                    }
                    // 天盘干
                    if (c === "sky") {
                        // 地利计算
                        let squareType = this.squareTypeMap["s" + s] // 宫属性
                        let skyGanType = this.getTianGanwuxing(value)
                        num = this.calcWxRelation(
                            skyGanType,
                            squareType,
                            "diLi"
                        )
                        // 地盘干关系计算
                        let earthGan = square["earth"]
                        if (earthGan) {
                            num += this.calcWxRelation(
                                earthGan,
                                skyGanType,
                                "diPanGan"
                            )
                        }
                    }
                }
                sum += num
            }
            return sum
        },
        /**
         * 获取天干五行属性
         */
        getTianGanwuxing(val) {
            let tiangan = [
                "甲",
                "乙",
                "丙",
                "丁",
                "戊",
                "己",
                "庚",
                "辛",
                "壬",
                "癸",
            ]
            let wuxing = ["木", "火", "土", "金", "水"]
            let tgIdx = tiangan.indexOf(val)
            let isEven = (num) => {
                return num % 2 === 0
            } // 判断偶数
            if (isEven(tgIdx)) {
                return wuxing[tgIdx / 2]
            } else {
                return wuxing[(tgIdx - 1) / 2]
            }
        },
        /**
         * 五行相生相克关系计算
         */
        calcWxRelation(self, other, type) {
            let wuxingRaw = ["木", "火", "土", "金", "水"] // 初始五行
            let selfIdx = wuxingRaw.indexOf(self)
            let wuxing = [
                ...wuxingRaw.slice(selfIdx),
                ...wuxingRaw.slice(0, selfIdx),
            ] // 以我为初始的五行循环
            selfIdx = 0 // 我处于初始位置
            let otherIdx = wuxing.indexOf(other)
            let num = Math.abs(selfIdx - otherIdx)
            if (type) {
                // 地利计算
                if (type === "diLi") {
                    let map = {
                        0: 30, // 我同宫--平顺有成(中吉),
                        1: 0, // 我生宫--多有消耗(小凶),
                        2: 10, // 我克宫--努力有得(小吉),
                        3: 0, // 宫克我--多阻无成(大凶),
                        4: 30, // 宫生我--得地支助(大吉),
                    }
                    return map[num]
                }
                // 地盘天干作用计算
                if (type === "diPanGan") {
                    let map = {
                        0: 30, // 地同天
                        1: 30, // 地生天
                        2: 0, // 地克天
                        3: 20, // 天克地
                        4: 20, // 天生地
                    }
                    return map[num]
                }
            } else {
                return num
            }
        },
    },
}
</script>

<style lang="less">
.qCalc {
    width: 92vh;
    height: 92vh;
    border: 1px solid #000;
    margin: 0px auto;
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    grid-template-rows: repeat(3, 1fr);

    .square {
        flex: 1;
        display: grid;
        grid-template-columns: repeat(3, 1fr);
        grid-template-rows: repeat(3, 1fr);
        position: relative;

        .el-input__inner {
            background-color: #f0f9eb;
            color: #000;
            font-size: 16px;
            font-weight: 500;
            text-align: center;
            padding: 0 10px;

            &::placeholder {
                font-size: 13px;
                font-weight: normal;
            }
        }

        .el-input--suffix .el-input__inner {
            padding-right: 30px;
        }

        // &:nth-child(odd) {
        //     background: #e0dfa7;
        // }

        // &:nth-child(even) {
        //     background: #a7e0a7;
        // }

        .cell {
            border-right: 1px solid #000;
            border-bottom: 1px solid #000;
            display: flex;
            flex-direction: column;
            justify-content: space-evenly;
            align-items: center;
            padding: 3px;

            .el-form-item {
                margin-bottom: 0;
            }
        }

        .square-type {
            position: absolute;
            font-size: 15vh;
            font-family: cursive;
            opacity: 0.1;
            transform: translate(-10px);
            pointer-events: none;
        }
    }

    .s1 {
        background: #a7e0a7;
    }

    .s2 {
        background: #FFA07A;
    }

    .s3 {
        background: #D2B48C;
    }

    .s4 {
        background: #a7e0a7;
    }

    .s5 {
        background: #D2B48C;
    }

    .s6 {
        background: #FFE4B5;
    }

    .s7 {
        background: #D2B48C;
    }

    .s8 {
        background: #B0E2FF;
    }

    .s9 {
        background: #FFE4B5;
    }

    .s5 {
        border-right: 1px solid #000;
        border-bottom: 1px solid #000;
    }

    .s3,
    .s6,
    .s9 {

        .c3,
        .c6,
        .c9 {
            border-right: none;
        }
    }

    .s7,
    .s8,
    .s9 {

        .c7,
        .c8,
        .c9 {
            border-bottom: none;
        }
    }

    .c1 {
        font-size: 40px;
    }
}

.el-select-dropdown__item {
    padding: 0 10px !important;
}

.red {
    color: red !important;

    .el-input__inner {
        color: red !important;
    }
}
</style>
