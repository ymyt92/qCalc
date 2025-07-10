<template>
    <el-form size="mini" :model="qCalcForm" label-position="left">
        <div class="qCalc">
            <div v-for="s in 9" :class="['square', 's' + s]">
                <template v-if="s !== 5">
                    <div class="cell c1">
                        <p class="sum">{{ getSquareScore(s) }}</p>
                        <el-popover placement="right-end" title="计分详情" width="480" trigger="click">
                            <el-table :data="sumDetailObj[s]" border stripe>
                                <el-table-column label="类型" prop="type"></el-table-column>
                                <el-table-column label="值" prop="value"></el-table-column>
                                <el-table-column label="分数" prop="score"></el-table-column>
                                <el-table-column label="备注" prop="mean" width="180"></el-table-column>
                            </el-table>
                            <i class="el-icon-view" slot="reference"></i>
                        </el-popover>
                    </div>
                    <div class="cell c2">
                        <el-form-item>
                            <el-select v-model="qCalcForm[`s${s}_shenZhu`]" clearable>
                                <el-option v-for="(value, key) in shenZhuMap" :key="key" :label="key" :value="key">
                                </el-option>
                            </el-select>
                        </el-form-item>
                    </div>
                    <div class="cell c3">
                        <div class="circle" v-show="qCalcForm[`s${s}_kongWang`]"></div>
                        <el-popover placement="bottom" title="配置" width="200" trigger="click" :append-to-body="false">
                            <el-form-item v-if="s === 3" label="遁甲" label-width="40px">
                                <el-select size="mini" v-model="dunjia" clearable @change="setDunjia">
                                    <el-option v-for="val in [
                                        '戊',
                                        '己',
                                        '庚',
                                        '辛',
                                        '壬',
                                        '癸',
                                    ]" :key="val" :label="val" :value="val">
                                    </el-option>
                                </el-select>
                            </el-form-item>
                            <el-form-item label="空亡">
                                <el-radio-group v-model="qCalcForm[`s${s}_kongWang`]">
                                    <el-radio :label="true">是</el-radio>
                                    <el-radio :label="undefined">否</el-radio>
                                </el-radio-group>
                            </el-form-item>
                            <i class="hai-setting el-icon-setting" slot="reference"></i>
                        </el-popover>
                    </div>
                    <div class="cell c4"></div>
                    <div class="cell c5">
                        <el-form-item>
                            <el-select v-model="qCalcForm[`s${s}_tianShi`]" clearable>
                                <el-option v-for="(value, key) in tianShiMap" :key="key" :label="key" :value="key">
                                </el-option>
                            </el-select>
                        </el-form-item>
                        <span class="square-type">{{
                            squareNameMap["s" + s]
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
                        <el-form-item :class="getTianGanColor(s, qCalcForm[`s${s}_sky`])">
                            <el-select v-model="qCalcForm[`s${s}_sky`]" clearable>
                                <el-option v-for="(value, key) in tianganMap" :key="key"
                                    :class="getTianGanColor(s, key)" :label="key" :value="key">
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
                        <el-form-item :class="{
                            red: menPoMap['s' + s].includes(
                                qCalcForm[`s${s}_renHe`]
                            ),
                        }">
                            <el-select v-model="qCalcForm[`s${s}_renHe`]" clearable>
                                <el-option v-for="(value, key) in renHeMap" :key="key" :class="{
                                    red: menPoMap['s' + s].includes(key),
                                }" :label="key" :value="key">
                                </el-option>
                            </el-select>
                        </el-form-item>
                    </div>
                    <div class="cell c9">
                        <el-form-item :class="getTianGanColor(s, qCalcForm[`s${s}_earth`])
                            ">
                            <el-select v-model="qCalcForm[`s${s}_earth`]" clearable>
                                <el-option v-for="(value, key) in tianganMap" :key="key"
                                    :class="getTianGanColor(s, key)" :label="key" :value="key">
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
            sumDetailObj: {},
            // 神助计分
            shenZhuMap: {
                值符: { mean: "吉神", score: 20 },
                太阴: { mean: "吉神", score: 20 },
                六合: { mean: "吉神", score: 20 },
                九天: { mean: "吉神", score: 20 },
                腾蛇: { mean: "凶神", score: 0 },
                白虎: { mean: "凶神", score: 0 },
                玄武: { mean: "凶神", score: 0 },
                九地: { mean: "吉神", score: 0 },
            },
            // 天时计分
            tianShiMap: {
                天任星: { mean: "吉星", score: 20 },
                天冲星: { mean: "吉星", score: 20 },
                天辅星: { mean: "吉星", score: 20 },
                天心星: { mean: "吉星", score: 20 },
                天禽星: { mean: "吉星", score: 20 },
                天蓬星: { mean: "凶星", score: 0 },
                天英星: { mean: "凶星", score: 0 },
                天芮星: { mean: "凶星", score: 0 },
                天柱星: { mean: "凶星", score: 0 },
            },
            // 人和计分
            renHeMap: {
                休门: { mean: "吉门", score: 30 },
                生门: { mean: "吉门", score: 30 },
                开门: { mean: "吉门", score: 30 },
                杜门: { mean: "平门", score: 0 },
                景门: { mean: "平门", score: 0 },
                死门: { mean: "凶门", score: 0 },
                惊门: { mean: "凶门", score: 0 },
                伤门: { mean: "凶门", score: 0 },
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
            // 九宫格名称
            squareNameMap: {
                s1: "巽",
                s2: "离",
                s3: "坤",
                s4: "震",
                s5: "中",
                s6: "兑",
                s7: "艮",
                s8: "艮",
                s9: "乾",
            },
            menPoMap: {
                s1: ["开门", "惊门"],
                s2: ["休门"],
                s3: ["伤门", "杜门"],
                s4: ["开门", "惊门"],
                s6: ["景门"],
                s7: ["伤门", "杜门"],
                s8: ["生门", "死门"],
                s9: ["景门"],
            },
            jiXingMap: {
                s1: ["壬", "癸"],
                s2: ["辛"],
                s3: ["己"],
                s4: ["戊"],
                s7: ["庚"],
            },
            ruMuMap: {
                s1: ["壬", "辛"],
                s3: ["甲", "癸"],
                s7: ["丁", "己", "庚"],
                s9: ["乙", "丙", "戊"],
            },
            xjmMap: {
                s1: ["壬"],
                s7: ["庚"],
            },
            dunjia: "",
        }
    },
    computed: {
        // 12周期级联选择
        zs12Casc() {
            let arr = Object.keys(this.zs12Map)
            let casc = arr.map((val, index) => {
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
            return casc
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
            let sIdx = "s" + s
            let square = this.squareScoreMap[sIdx]
            let sum = 200
            let scoreTypeMap = {
                tianShi: "天时",
                diLi: "地利",
                renHe: "人和",
                shenZhu: "神助",
                zs12: "长生十二宫",
                diPanGan: "天干作用",
                menPo: "门迫",
                jiXing: "击刑",
                ruMu: "入墓",
                kongWang: "空亡",
            }
            let rowMap = {}
            for (let c in square) {
                let value = square[c]
                let num = 0
                if ((value && value.length) || value == true) {
                    if (c === "tianShi") {
                        num = this.tianShiMap[value].score
                        rowMap[c] = {
                            type: scoreTypeMap[c],
                            value,
                            score: num,
                            mean: this.tianShiMap[value].mean,
                        }
                    }
                    if (c === "renHe") {
                        num = this.renHeMap[value].score
                        rowMap[c] = {
                            type: scoreTypeMap[c],
                            value,
                            score: num,
                            mean: this.renHeMap[value].mean,
                        }
                        // 门迫
                        if (this.menPoMap[sIdx].includes(value)) {
                            rowMap["menPo"] = {
                                type: scoreTypeMap["menPo"],
                                value: `${value}在${this.squareNameMap[sIdx]}宫`,
                                score: -20,
                                mean: "门克宫",
                            }
                            num += -20
                        }
                    }
                    if (c === "shenZhu") {
                        num = this.shenZhuMap[value].score
                        rowMap[c] = {
                            type: scoreTypeMap[c],
                            value,
                            score: num,
                            mean: this.shenZhuMap[value].mean,
                        }
                    }
                    if (c === "zs12") {
                        let temp = this.zs12Map[value[0]]
                        let last = value[1]
                        if (last) {
                            temp += this.zs12Map[last]
                        }
                        num = temp / value.length // 取平均值
                        rowMap[c] = {
                            type: scoreTypeMap[c],
                            value: value.join(""),
                            score: num,
                            mean: "",
                        }
                    }
                    // 天盘干
                    if (c === "sky") {
                        // 地利计算
                        let squareType = this.squareTypeMap[sIdx] // 宫属性
                        let skyGanType = this.getTianGanwuxing(value)
                        let rel = this.calcWxRelation(
                            skyGanType,
                            squareType,
                            "diLi"
                        )
                        num = rel.score
                        rowMap["diLi"] = {
                            type: scoreTypeMap["diLi"],
                            value: `我:${skyGanType},宫:${squareType}`,
                            score: num,
                            mean: rel.text,
                        }

                        // 地盘干关系计算
                        let earthGan = square["earth"]
                        if (earthGan) {
                            let earthGanType = this.getTianGanwuxing(earthGan)
                            let rel = this.calcWxRelation(
                                earthGanType,
                                skyGanType,
                                "diPanGan"
                            )
                            let _num = rel.score
                            rowMap["diPanGan"] = {
                                type: scoreTypeMap["diPanGan"],
                                value: `天:${skyGanType},地:${earthGanType}`,
                                score: num,
                                mean: rel.text,
                            }
                            num += _num
                        }
                        // 击刑计算
                        if (this.jiXingMap[sIdx]) {
                            let skyJx = false // 天盘干击刑
                            skyJx = this.jiXingMap[sIdx].includes(value)
                            if (skyJx) {
                                rowMap["jiXing"] = {
                                    type: scoreTypeMap["jiXing"],
                                    value: `${value}在${this.squareNameMap[sIdx]}宫`,
                                    score: -20,
                                    mean: "天盘干击刑",
                                }
                                num += -20
                            } else {
                                // 只有地盘干击刑
                                if (earthGan) {
                                    let earthJx =
                                        this.jiXingMap[sIdx].includes(earthGan)
                                    if (earthJx) {
                                        rowMap["jiXing"] = {
                                            type: scoreTypeMap["jiXing"],
                                            value: `${earthGan}在${this.squareNameMap[sIdx]}宫`,
                                            score: -10,
                                            mean: "地盘干击刑",
                                        }
                                        num += -10
                                    }
                                }
                            }
                        }

                        // 入墓计算
                        if (this.ruMuMap[sIdx]) {
                            let skyRm = false // 天盘干入墓
                            skyRm = this.ruMuMap[sIdx].includes(value)
                            if (skyRm) {
                                rowMap["ruMu"] = {
                                    type: scoreTypeMap["ruMu"],
                                    value: `${value}在${this.squareNameMap[sIdx]}宫`,
                                    score: -30,
                                    mean: "天盘干入墓",
                                }
                                // 坤宫入墓
                                if (s === 3) {
                                    if (this.dunjia) {
                                        rowMap["ruMu"].mean += `(遁甲:${this.dunjia})`
                                    }
                                }
                                num += -30
                            } else {
                                // 只有地盘干入墓
                                if (earthGan) {
                                    let earthRm =
                                        this.ruMuMap[sIdx].includes(earthGan)
                                    if (earthRm) {
                                        rowMap["ruMu"] = {
                                            type: scoreTypeMap["ruMu"],
                                            value: `${earthGan}在${this.squareNameMap[sIdx]}宫`,
                                            score: -15,
                                            mean: "地盘干入墓",
                                        }
                                        // 坤宫入墓
                                        if (s === 3) {
                                            if (this.dunjia) {
                                                rowMap["ruMu"].mean += `(遁甲:${this.dunjia})`
                                            }
                                        }
                                        num += -15
                                    }
                                }
                            }
                        }
                    }
                    // 空亡
                    if (c === "kongWang") {
                        rowMap["kongWang"] = {
                            type: scoreTypeMap["kongWang"],
                            value: "是",
                            score: -30,
                            mean: "",
                        }
                        num += -30
                    }
                }

                sum += num
            }
            let scoreTable = [{ type: "基础分", score: 200 }]
            for (let key in scoreTypeMap) {
                let row = rowMap[key]
                if (row) {
                    scoreTable.push(row)
                }
            }
            this.sumDetailObj[s] = scoreTable
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
                        0: { score: 30, text: "我同宫--平顺有成(中吉)" },
                        1: { score: 0, text: "我生宫--多有消耗(小凶)" },
                        2: { score: 10, text: "我克宫--努力有得(小吉)" },
                        3: { score: 0, text: "宫克我--多阻无成(大凶)" },
                        4: { score: 30, text: "宫生我--得地支助(大吉)" },
                    }
                    return map[num]
                }
                // 地盘天干作用计算
                if (type === "diPanGan") {
                    let map = {
                        0: { score: 30, text: "地同天" },
                        1: { score: 30, text: "地生天" },
                        2: { score: 0, text: "地克天" },
                        3: { score: 20, text: "天克地" },
                        4: { score: 20, text: "天生地" },
                    }
                    return map[num]
                }
            } else {
                return num
            }
        },
        /**
         * 获取天干害颜色
         * @param s 宫格序号
         */
        getTianGanColor(s, val) {
            if (!val) {
                return ""
            }
            let sIdx = "s" + s
            let maps = [this.xjmMap, this.jiXingMap, this.ruMuMap]
            let obj = maps.find((item) => {
                let isHai = false
                if (item[sIdx] && item[sIdx].includes(val)) {
                    isHai = true
                }
                return isHai
            })
            if (obj) {
                // 首先判断是否判断击刑或者入墓
                let idx = maps.indexOf(obj)
                let colors = {
                    0: "blue",
                    1: "purple",
                    2: "green",
                }
                return colors[idx]
            } else {
                return ""
            }
        },
        setDunjia(val) {
            if (this.xjmMap.s3) {
                delete this.xjmMap.s3
            }
            if (val) {
                if (!this.ruMuMap.s3.includes(val)) {
                    this.ruMuMap.s3 = ["甲", "癸", val]
                }
                if (val === "己") {
                    this.xjmMap.s3 = ["己"]
                }
            } else {
                this.ruMuMap.s3 = ["甲", "癸"]
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

        .el-input {
            i {
                color: #88898b;
            }
        }

        .el-input__suffix {
            color: #88898b;
        }

        .el-input__inner {
            background-color: unset;
            color: #000;
            font-size: 18px;
            font-weight: 500;
            text-align: center;
            padding: 0 10px;
            border: none;

            &::placeholder {
                color: #88898b;
                font-size: 13px;
                font-weight: normal;
            }
        }

        .el-input__icon {
            width: 22px;
        }

        .el-input__suffix {
            right: 0px;
        }

        .el-input--suffix .el-input__inner {
            padding-right: 25px;
        }

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
        background: #ffcee1;
    }

    .s3 {
        background: #d2b48c;
    }

    .s4 {
        background: #a7e0a7;
    }

    .s5 {
        background: #d2b48c;
    }

    .s6 {
        background: #ffffed;
    }

    .s7 {
        background: #d2b48c;
    }

    .s8 {
        background: #b0e2ff;
    }

    .s9 {
        background: #ffffed;
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
        position: relative;

        .sum {
            font-size: 40px;
        }

        i {
            color: #007dff;
            cursor: pointer;
            position: absolute;
            right: 10px;
            bottom: 10px;
            font-weight: 800;
        }
    }

    .c3 {
        position: relative;

        .hai-setting {
            color: #007dff;
            cursor: pointer;
            position: absolute;
            right: 10px;
            bottom: 10px;
        }

        .circle {
            height: 40px;
            width: 40px;
            border: 1px solid #000;
            border-radius: 50%;
            background: #fff;
        }
    }
}

.sum-detail {
    white-space: pre-wrap;
    line-height: 1.5;
}

.el-select-dropdown__item {
    padding: 0 10px !important;
}

.selected {
    color: #000000 !important;
    font-weight: bolder !important;
}

.el-cascader-menu__wrap {
    height: fit-content !important;
}

.el-scrollbar__wrap {
    height: fit-content !important;
}

.el-select-dropdown__wrap {
    max-height: unset !important;
}

.red {
    color: red !important;
    font-weight: 500;

    .el-input__inner {
        color: red !important;
    }
}

.purple {
    color: #d018d0 !important;
    font-weight: 500;

    .el-input__inner {
        color: #d018d0 !important;
    }
}

.green {
    color: green !important;
    font-weight: 500;

    .el-input__inner {
        color: green !important;
    }
}

.blue {
    color: blue !important;
    font-weight: 500;

    .el-input__inner {
        color: blue !important;
    }
}
</style>
