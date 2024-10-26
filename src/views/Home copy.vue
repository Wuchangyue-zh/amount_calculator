<template>
    <div>
        <el-row :gutter="20">
            <el-col :span="18" id="col18">
                <el-row :gutter="20">
                    <!-- <el-checkbox-group v-model="checkNum"> -->
                        <el-col :span="4" v-for="(item, index) in tableData" :key="index">
                            <div style="display: flex;align-items: center;">
                                <div class="icon_1" :style="'background-color:'+ item.color">{{ item.num }}</div>
                                <div style="width: 80%;">
                                    <div>
                                        <span style="padding-right: 10px;">{{ item.name }}</span>
                                        <span>{{ item.amount }}元</span>
                                    </div>
                                    <!-- <div style="display: flex;justify-content: space-between;">
                                        <div>
                                            <span style="padding-right: 10px;">{{ item.name }}</span>
                                            <span>{{ item.amount }}元</span>
                                        </div>
                                        <el-checkbox :value="item.num">
                                            <div>
                                                <span style="padding-right: 10px;">{{ item.name }}</span>
                                                <span>{{ item.amount }}元</span>
                                            </div>
                                        </el-checkbox>
                                    </div> -->
                                    <el-progress :text-inside="true" :stroke-width="20" :percentage="item.percentage" :color="item.color" text-color="#fff"></el-progress>
                                </div>
                                
                            </div>
                        </el-col>
                    <!-- </el-checkbox-group> -->

                </el-row>
                <el-divider></el-divider>
                <el-row :gutter="20">
                    <el-col :span="4" v-for="(item, index) in zodiacsData" :key="index">
                        <div style="display: flex;justify-content: flex-start;align-items: center;">
                            <div class="icon_1" :style="'background-color:'+ item.color">{{ item.name }}</div>
                            <div>{{ item.amount }}元</div>
                        </div>
                        <el-progress :text-inside="true" :stroke-width="20" :percentage="item.percentage" :color="item.color" text-color="#fff"></el-progress>
                    </el-col>
                </el-row>


            </el-col>
            <el-col :span="6">
                <el-table :data="localData" size="mini" border stripe style="width: 100%" :height="col18Height">
                    <el-table-column prop="num" align="center" label="号码"></el-table-column>
                    <el-table-column prop="money" align="center" label="各（元）"></el-table-column>
                    <el-table-column label="操作" align="center">
                        <template slot="header">
                            <el-button type="warning" size="mini" @click="cleanLocal">重置</el-button>
                        </template>
                        <template slot-scope="scope">
                            <!-- <el-button type="primary" size="mini" @click="handleEdit(scope.row)">编辑</el-button> -->
                            <el-button type="danger" size="mini" @click="handleDelete(scope.row)">删除</el-button>
                        </template>
                    </el-table-column>
                </el-table>
            </el-col>
        </el-row>
        <el-divider></el-divider>
        <div style="display: flex;justify-content: space-between;align-items: flex-start;">
            <div>
                <!-- 号码 -->
                <el-form :inline="true" :model="form" :rules="rules" ref="form">
                    <el-form-item label="号码" prop="num">
                        <!-- <el-input v-model="form.num" placeholder="例如：10，21，33"></el-input> -->
                        <el-select v-model="form.num" filterable placeholder="例如：10，21，33" multiple>
                            <el-option
                                v-for="item in tableData"
                                :key="item.num"
                                :label="item.num"
                                :value="item.num">
                                <span style="float: left">{{ item.num }}</span>
                                <span style="float: right">{{ item.name }}</span>
                            </el-option>
                        </el-select>
                    </el-form-item>
                    <el-form-item label="各" prop="money">
                        <el-input v-model="form.money" placeholder="请输入金额">
                            <template slot="append">元</template>
                        </el-input>
                    </el-form-item>
                    <el-form-item>
                        <el-button type="primary" @click="calculate('HM')">计算</el-button>
                    </el-form-item>
                </el-form>
                <!-- 生肖 -->
                <el-form :inline="true" :model="formSX" :rules="rules" ref="formSX">
                    <el-form-item label="生肖" prop="num">
                        <el-input v-model="formSX.num" placeholder="例如：鼠，牛，虎"></el-input>
                    </el-form-item>
                    <el-form-item label="各" prop="money">
                        <el-input v-model="form.money" placeholder="请输入金额">
                            <template slot="append">元</template>
                        </el-input>
                    </el-form-item>
                    <el-form-item>
                        <el-button type="primary" @click="calculate('SX')">计算</el-button>
                    </el-form-item>
                </el-form>
                <div><span style="font-size: 24px;font-weight: bold;">总数：{{ amount }} 元</span></div>
            </div>
            <!-- <div><span style="font-size: 24px;font-weight: bold;">总数：{{ amount }} 元</span></div> -->
        </div>

    </div>
</template>

<script>
// const fs = require('fs');
// import path from 'path-browserify'

export default {
    name: 'HomeView',
    data() {
        return {
            zodiacs: [
            {
                    name: '鸡',
                    color: '#FF8C00' // 深橙色
                },
                {
                    name: '狗',
                    color: '#FF6347' // 番茄色
                },
                {
                    name: '猪',
                    color: '#FF4500' // 橙红色
                },
                {
                    name: '鼠',
                    color: '#DA70D6' // 兰花紫
                },
                {
                    name: '牛',
                    color: '#BA55D3' // 中紫红
                },
                {
                    name: '虎',
                    color: '#9370DB' // 中紫色
                },
                {
                    name: '兔',
                    color: '#8A2BE2' // 蓝紫色
                },
                {
                    name: '龙',
                    color: '#7B68EE' // 中板蓝
                },
                {
                    name: '蛇',
                    color: '#6A5ACD' // 板岩蓝
                },
                {
                    name: '马',
                    color: '#483D8B' // 暗岩蓝
                },
                {
                    name: '羊',
                    color: '#4169E1' // 皇家蓝
                },
                {
                    name: '猴',
                    color: '#4682B4' // 钢蓝
                }
            ],
            tableData: [],
            zodiacsData: [],
            form: {
                num: '',
                money: ''
            },
            formSX: {
                num: '',
                money: ''
            },
            rules: {
                num: [
                    { required: true, message: '请输入号码', trigger: 'blur' }
                ],
                money: [
                    { required: true, message: '请输入金额', trigger: 'blur' }
                ]
            },
            localData: [],
            col18Height: 600,
            amount: 0,
            checkNum: []
        }
    },
    mounted() {
        // 计算1-49岁生肖
        this.calculateZodiacs()
        // 计算生肖金额
        this.calculateAmount()
        // 计算表格高度
        this.$nextTick(() => {
            this.calculateCol18Height()
        })
        // 从本地存储中获取数据
        this.init()
    },
    methods: {
        init() {
            if (localStorage.getItem('time')) {
                const time = localStorage.getItem('time')
                const currentTime = new Date().getTime()
                const diffTime = currentTime - time
                if (diffTime > 100000 * 60 * 10) {
                    localStorage.removeItem('localData')
                } else {
                    const localData = localStorage.getItem('localData')
                    if (localData && localData !== '[]') {
                        this.localData = JSON.parse(localData)
                        this.localData.forEach(item => {
                            this.amount += item.amount
                            const numArr = item.num.split(',')
                            this.AmountByNum(numArr, item.money, false)
                        })
                    }
                }
            } else {
                localStorage.setItem('time', new Date().getTime())
            }
        },
        getZodiac(year) {
            return this.zodiacs[year % 12]
        },
        calculateZodiacs() {
            let arr = []
            const currentYear = this.$moment().year()
            for (let age = 1; age <= 49; age++) {
                
                const birthYear = currentYear - age
                const obj = {
                    num: age,
                    name: this.getZodiac(birthYear).name,
                    amount: 0,
                    percentage: 0,
                    color: this.getZodiac(birthYear).color
                }
                arr.push(obj)
            }
            this.tableData = arr
        },
        calculateAmount() {
            const arr2 = []
            for (let i = 0; i < this.zodiacs.length; i++) {
                const obj = {
                    name: this.zodiacs[i].name,
                    amount: 0,
                    percentage: 0,
                    color: this.zodiacs[i].color
                }
                arr2.push(obj)
            }
            arr2.sort((a, b) => {
                const zodiacs = ['鼠', '牛', '虎', '兔', '龙', '蛇', '马', '羊', '猴', '鸡', '狗', '猪']
                return zodiacs.indexOf(a.name) - zodiacs.indexOf(b.name)
            })
            this.zodiacsData = arr2
        },
        calculate(type) {
            if (type === 'HM') {
                this.$refs['form'].validate((valid) => {
                    if (valid) {
                        this.checkValidHM()
                    } else {
                        this.$message.error('请输入正确的数字和金额')
                    }
                })
            } else if (type === 'SX') {
                this.$refs['formSX'].validate((valid) => {
                    if (valid) {
                        this.checkValidSX()
                    } else {
                        this.$message.error('请输入正确的生肖和金额')
                    }
                })
            }

        },
        replaceChinesePunctuation(input) {
            // 定义正则表达式，匹配中文逗号、分号、句号和空格
            const regex = /[，；;.。 ]/g
            // 使用 replace 方法将匹配的字符替换为英文逗号
            const result = input.replace(regex, ',')
            // 转换为数组
            const arr = result.split(',')
            // 去掉数组中的空字符串
            const arr2 = arr.filter(item => item !== '')
            return arr2
        },
        // 校验表单
        checkValidHM() {
            let num = this.form.num
            let money = this.form.money

            console.log('checkValidHM', num)
            // 判断num是否为数组
            if (!Array.isArray(num)) {
                // 使用正则表达式去掉空格，并且将中文逗号替换为英文逗号
                num = num.replace(/\s+/g, '').replace(/，/g, ',')
                // 转换为数组
                let numArr = num.split(',')
                // 去掉数组中的空字符串
                numArr = numArr.filter(item => item !== '')
                // 判断输入的字符串是否在1-49之间
                const isNum = numArr.every(item => {
                    return item >= 1 && item <= 49
                })
                // isNum为false时，提示用户输入错误
                if (!isNum) {
                    this.$message.error('请输入1-49之间的数字')
                    return
                }
                // 判断是否有重复的数字
                const isRepeat = numArr.some((item, index) => {
                    return numArr.indexOf(item) !== index
                })
                if (isRepeat) {
                    this.$message.error('发现重复的号码，请重新输入')
                    return
                }
                num = numArr
            }
            // 判断输入的金额是否为数字
            if (isNaN(money)) {
                this.$message.error('请输入正确的金额')
                return
            }
            // 将金额转换为数字
            money = Number(money)
            // 金额不能为0
            if (money === 0) {
                this.$message.error('金额不能为0')
                return
            }
            // 计算金额
            this.AmountByNum(num, money, true)
        },
        checkValidSX() {
            let num = this.formSX.num
            let money = this.formSX.money

            // 使用正则表达式将中文逗号,分号，句号，空格替换为英文逗号
            let numArr = this.replaceChinesePunctuation(num)
            // 判断输入的字符串是否在生肖之间
            const isNum = numArr.every(item => {
                return ['鼠', '牛', '虎', '兔', '龙', '蛇', '马', '羊', '猴', '鸡', '狗', '猪'].includes(item)
            })
            // isNum为false时，提示用户输入错误
            if (!isNum) {
                this.$message.error('请输入正确的生肖')
                return
            }
            // 判断输入的金额是否为数字
            if (isNaN(money)) {
                this.$message.error('请输入正确的金额')
                return
            }
            // 将金额转换为数字
            money = Number(money)
            // 金额不能为0
            if (money === 0) {
                this.$message.error('金额不能为0')
                return
            }
            // 计算金额
            this.AmountByNum(numArr, money, true)
        },
        AmountByNum(numArr, money, status) {
            // 计算总金额
            this.amount += numArr.length * money
            // 计算各个生肖的金额
            numArr.forEach(item => {
                const zodiac = this.getZodiac(this.$moment().year() - item)
                const index = this.zodiacsData.findIndex(z => z.name === zodiac.name)
                this.zodiacsData[index].amount += Number(money)
            })
            // 计算各个年龄段的金额
            numArr.forEach(item => {
                const index = this.tableData.findIndex(z => z.num === Number(item))
                this.tableData[index].amount += Number(money)
            })
            // 计算各个生肖的百分比
            this.zodiacsData.forEach(item => {
                item.percentage = parseFloat((item.amount / this.amount * 100).toFixed(2))
            })
            // 计算各个年龄段的百分比
            this.tableData.forEach(item => {
                item.percentage = parseFloat((item.amount / this.amount * 100).toFixed(2))
            })
            const obj = {
                num: numArr.join(','),
                money: money,
                amount: numArr.length * money,
                date: this.$moment().format('YYYY-MM-DD HH:mm:ss')
            }
            // 更新表格数据
            // this.localData.push(obj)
            // 将数据存储到本地
            if (status) {
                this.localData.push(obj)
                localStorage.setItem('localData', JSON.stringify(this.localData))
            }
        },
        calculateCol18Height() {
            const col18Element = document.getElementById('col18');
            if (col18Element) {
                this.col18Height = col18Element.clientHeight
            }
        },
        handleDelete(row) {
            const index = this.localData.findIndex(item => item.num === row.num && item.money === row.money)
            this.localData.splice(index, 1)
            this.amount -= row.amount
            const numArr = row.num.split(',')
            numArr.forEach(item => {
                const zodiac = this.getZodiac(this.$moment().year() - item)
                const index = this.zodiacsData.findIndex(z => z.name === zodiac.name)
                this.zodiacsData[index].amount -= Number(row.money)
            })
            numArr.forEach(item => {
                const index = this.tableData.findIndex(z => z.num === Number(item))
                this.tableData[index].amount -= Number(row.money)
            })
            this.zodiacsData.forEach(item => {
                if (this.amount === 0) {
                    item.percentage = 0
                } else {
                    item.percentage = parseFloat((item.amount / this.amount * 100).toFixed(2))
                }
            })
            this.tableData.forEach(item => {
                if (this.amount === 0) {
                    item.percentage = 0
                } else {
                    item.percentage = parseFloat((item.amount / this.amount * 100).toFixed(2))
                }
            })
            localStorage.setItem('localData', JSON.stringify(this.localData))
        },
        cleanLocal() {
            this.localData = []
            this.amount = 0
            this.zodiacsData.forEach(item => {
                item.amount = 0
                item.percentage = 0
            })
            this.tableData.forEach(item => {
                item.amount = 0
                item.percentage = 0
            })
            localStorage.removeItem('localData')
        }
    }
}
</script>
<style>
.num_box {
    display: inline-block;
    margin: 10px;
}
.icon_1 {
    padding: 10px;
    margin: 10px;
    color: #fff;
    font-size: 20px;
    border-radius: 50%;
    width: 25px;
    height: 25px;
    text-align: center;
}
</style>