<template>
    <div>
        <el-row :gutter="20">
            <el-col :span="18" id="col18">
                <el-divider content-position="left">号码</el-divider>
                <div class="first_box">
                    <el-checkbox-group v-model="checkHM" @change="changeHM">
                        <div v-for="(item, index) in tableData" :key="index" class="first_box_item">
                            <el-checkbox-button :label="item.num" style="width: 100%;">
                                <div style="display: flex; align-items: center; width: 100%;">
                                    <div class="icon_1" :style="'background-color:'+ item.color">{{ item.num }}</div>
                                    <div style="width: 80%;">
                                        <div>
                                            <span style="padding-right: 10px;">{{ item.name }}</span>
                                            <span>{{ item.amount }}元</span>
                                        </div>
                                        <el-progress :text-inside="true" :stroke-width="20" :percentage="item.percentage" :color="item.color" text-color="#fff"></el-progress>
                                    </div>
                                </div>
                            </el-checkbox-button>
                        </div>
                    </el-checkbox-group>
                </div>
                <el-divider content-position="left">生肖</el-divider>
                <el-row>
                    <el-checkbox-group v-model="checkSX" @change="changeSX">
                        <el-col :span="4" v-for="(item, index) in zodiacsData" :key="index">
                            <el-checkbox-button :label="item.name" style="width: 100%;">
                                <div style="display: flex;justify-content: flex-start;align-items: center;">
                                    <div class="icon_1" :style="'background-color:'+ item.color">{{ item.name }}</div>
                                    <div style="width: 80%;">
                                        <div>{{ item.amount }}元</div>
                                        <el-progress :text-inside="true" :stroke-width="20" :percentage="item.percentage" :color="item.color" text-color="#fff"></el-progress>
                                    </div>
                                </div>
                            </el-checkbox-button>
                        </el-col>
                    </el-checkbox-group>
                </el-row>
            </el-col>
            <el-col :span="6">
                <el-table :data="localData" size="mini" border stripe style="width: 100%" :height="col18Height">
                    <el-table-column prop="num" align="center" label="记录"></el-table-column>
                    <el-table-column prop="money" align="center" label="各（元）"></el-table-column>
                    <el-table-column prop="amount" align="center" label="总金额"></el-table-column>
                    <el-table-column prop="date" align="center" label="日期"></el-table-column>
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
                        <el-input v-model="form.num" placeholder="例如：10，21，33"></el-input>
                    </el-form-item>
                    <el-form-item label="各" prop="money">
                        <el-input v-model="form.money" placeholder="请输入金额">
                            <template slot="append">元</template>
                        </el-input>
                    </el-form-item>
                    <el-form-item>
                        <el-button type="primary" @click="calculate('HM')">提交</el-button>
                    </el-form-item>
                </el-form>
                <!-- 生肖 -->
                <el-form :inline="true" :model="formSX" :rules="rules" ref="formSX">
                    <el-form-item label="生肖" prop="name">
                        <el-input v-model="formSX.name" placeholder="例如：鼠，牛，虎"></el-input>
                    </el-form-item>
                    <el-form-item label="各" prop="money">
                        <el-input v-model="formSX.money" placeholder="请输入金额">
                            <template slot="append">元</template>
                        </el-input>
                    </el-form-item>
                    <el-form-item>
                        <el-button type="primary" @click="calculate('SX')">提交</el-button>
                    </el-form-item>
                </el-form>
                <el-form :inline="true" :model="formBS" :rules="rules" ref="formBS">
                    <el-form-item label="波色" prop="color">
                        <el-checkbox-group v-model="formBS.color">
                            <el-checkbox label="红">红</el-checkbox>
                            <el-checkbox label="蓝">蓝</el-checkbox>
                            <el-checkbox label="绿">绿</el-checkbox>
                        </el-checkbox-group>
                    </el-form-item>
                    <el-form-item label="各" prop="money">
                        <el-input v-model="formBS.money" placeholder="请输入金额">
                            <template slot="append">元</template>
                        </el-input>
                    </el-form-item>
                    <el-form-item>
                        <el-button type="primary" @click="calculate('BS')">提交</el-button>
                    </el-form-item>
                </el-form>
                <div style="font-size: 24px;font-weight: bold;">总数：{{ amount }} 元</div>
            </div>
            <div style="width: 50%;">
                <el-divider content-position="left">排序</el-divider>
                <div :span="2" v-for="(item, index) in amountTable" :key="index" class="num_box">
                    <div class="icon_2" :style="'background-color:'+ item.color">{{ item.num }}</div>
                    <div>{{ item.amount }}元</div>
                </div>
            </div>
        </div>

    </div>
</template>

<script>

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
            tableData: [
                {
                    num: 1,
                    color: 'red'
                },
                {
                    num: 2,
                    color: 'red'
                },
                {
                    num: 3,
                    color: 'blue'
                },
                {
                    num: 4,
                    color: 'blue'
                },
                {
                    num: 5,
                    color: 'green'
                },
                {
                    num: 6,
                    color: 'green'
                },
                {
                    num: 7,
                    color: 'red'
                },
                {
                    num: 8,
                    color: 'red'
                },
                {
                    num: 9,
                    color: 'blue'
                },
                {
                    num: 10,
                    color: 'blue'
                },
                {
                    num: 11,
                    color: 'green'
                },
                {
                    num: 12,
                    color: 'red'
                },
                {
                    num: 13,
                    color: 'red'
                },
                {
                    num: 14,
                    color: 'blue'
                },
                {
                    num: 15,
                    color: 'blue'
                },
                {
                    num: 16,
                    color: 'green'
                },
                {
                    num: 17,
                    color: 'green'
                },
                {
                    num: 18,
                    color: 'red'
                },
                {
                    num: 19,
                    color: 'red'
                },
                {
                    num: 20,
                    color: 'red'
                },
                {
                    num: 21,
                    color: 'green'
                },
                {
                    num: 22,
                    color: 'green'
                },
                {
                    num: 23,
                    color: 'red'
                },
                {
                    num: 24,
                    color: 'red'
                },
                {
                    num: 25,
                    color: 'blue'
                },
                {
                    num: 26,
                    color: 'blue'
                },
                {
                    num: 27,
                    color: 'green'
                },
                {
                    num: 28,
                    color: 'green'
                },
                {
                    num: 29,
                    color: 'red'
                },
                {
                    num: 30,
                    color: 'red'
                },
                {
                    num: 31,
                    color: 'blue'
                },
                {
                    num: 32,
                    color: 'green'
                },
                {
                    num: 33,
                    color: 'green'
                },
                {
                    num: 34,
                    color: 'red'
                },
                {
                    num: 35,
                    color: 'red'
                },
                {
                    num: 36,
                    color: 'blue'
                },
                {
                    num: 37,
                    color: 'blue'
                },
                {
                    num: 38,
                    color: 'green'
                },
                {
                    num: 39,
                    color: 'green'
                },
                {
                    num: 40,
                    color: 'red'
                },
                {
                    num: 41,
                    color: 'blue'
                },
                {
                    num: 42,
                    color: 'blue'
                },
                {
                    num: 43,
                    color: 'green'
                },
                {
                    num: 44,
                    color: 'green'
                },
                {
                    num: 45,
                    color: 'red'
                },
                {
                    num: 46,
                    color: 'red'
                },
                {
                    num: 47,
                    color: 'blue'
                },
                {
                    num: 48,
                    color: 'blue'
                },
                {
                    num: 49,
                    color: 'green'
                }
            ],
            zodiacsData: [],
            form: {
                num: '',
                money: ''
            },
            formSX: {
                name: '',
                money: ''
            },
            formBS: {
                color: [],
                money: ''
            },
            rules: {
                num: [
                    { required: true, message: '请输入号码', trigger: 'blur' }
                ],
                money: [
                    { required: true, message: '请输入金额', trigger: 'blur' }
                ],
                name: [
                    { required: true, message: '请输入内容', trigger: 'blur' }
                ],
                color: [
                    { type: 'array', required: true, message: '请选择波色', trigger: 'change' }
                ]
            },
            localData: [],
            col18Height: 600,
            amount: 0,
            checkHM: [],
            checkSX: [],
            checkBS: [],
            amountTable: []
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
                if (diffTime > 1000 * 60 * 60 * 24 * 3) {
                    localStorage.removeItem('localData')
                    localStorage.setItem('time', new Date().getTime())
                    // 关闭页面
                    // window.close()
                } else {
                    const localData = localStorage.getItem('localData')
                    if (localData && localData !== '[]') {
                        this.localData = JSON.parse(localData)
                        this.localData.forEach(item => {
                            const numArr = item.num.split(',')
                            if (item.type === 'num') {
                                this.AmountByNum(numArr, item.money, false)
                            } else if (item.type === 'sx') {
                                this.AmountBySX(numArr, item.money, false)
                            } else if (item.type === 'bs') {
                                this.AmountByBS(numArr, item.money, false)
                            }
                        })
                        this.sortTable()
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
                    color: this.tableData[age - 1].color
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
                    console.log('SX_valid', valid)
                    if (valid) {
                        this.checkValidSX()
                    } else {
                        this.$message.error('请输入正确的生肖和金额')
                    }
                })
            } else if (type === 'BS') {
                this.$refs['formBS'].validate((valid) => {
                    if (valid) {
                        this.checkValidBS()
                    } else {
                        this.$message.error('请输入正确的波色和金额')
                    }
                })
            }
            this.sortTable()
        },
        replaceChinesePunctuation(input) {
            // 定义正则表达式，匹配中文逗号、分号、句号、|和空格
            const regex = /[+\-*/\\、，；;.。 ]/g
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

            let numArr = this.replaceChinesePunctuation(num)
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
            // 清空表单
            this.checkHM = []
            this.$refs['form'].resetFields()
        },
        checkValidSX() {
            let num = this.formSX.name
            let money = this.formSX.money

            // 使用正则表达式将中文逗号,分号，句号，空格替换为英文逗号
            let numArr = this.replaceChinesePunctuation(num)
            console.log('checkValidSX_numArr', numArr)
            // 判断输入的字符串是否在生肖之间
            const isNum = numArr.every(item => {
                return ['鼠', '牛', '虎', '兔', '龙', '蛇', '马', '羊', '猴', '鸡', '狗', '猪'].includes(item)
            })
            console.log('checkValidSX_isNum', isNum)
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
            this.AmountBySX(numArr, money, true)
            // 清空表单
            this.checkSX = []
            this.$refs['formSX'].resetFields()
        },
        checkValidBS() {
            let color = this.formBS.color
            let money = this.formBS.money

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
            this.AmountByBS(color, money, true)
            // 清空表单
            this.checkBS = []
            this.$refs['formBS'].resetFields()
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
            
            if (status) {
                const obj = {
                    num: numArr.join(','),
                    money: money,
                    amount: numArr.length * money,
                    date: this.$moment().format('YYYY-MM-DD HH:mm:ss'),
                    type: 'num'
                }
                // 更新表格数据
                this.localData.push(obj)
                // 将数据存储到本地
                localStorage.setItem('localData', JSON.stringify(this.localData))
            }
        },
        AmountBySX(numArr, money, status) {
            // 计算总金额
            this.amount += this.sxAmount(numArr, money)

            let temp = []
            // 计算各个年龄段属于的生肖的金额
            this.tableData.forEach(item => {
                if (numArr.includes(item.name)) {
                    item.amount += Number(money)
                    temp.push(item.name)
                }
            })
            // 计算各个年龄段的百分比
            this.tableData.forEach(item => {
                item.percentage = parseFloat((item.amount / this.amount * 100).toFixed(2))
            })

            // 计算各个生肖的金额
            temp.forEach(item => {
                const index = this.zodiacsData.findIndex(z => z.name === item)
                this.zodiacsData[index].amount += Number(money)
            })
            // 计算各个生肖的百分比
            this.zodiacsData.forEach(item => {
                item.percentage = parseFloat((item.amount / this.amount * 100).toFixed(2))
            })

            // 将数据存储到本地
            if (status) {
                const obj = {
                    num: numArr.join(','),
                    money: money,
                    amount: this.sxAmount(numArr, money),
                    date: this.$moment().format('YYYY-MM-DD HH:mm:ss'),
                    type: 'sx'
                }
                this.localData.push(obj)
                localStorage.setItem('localData', JSON.stringify(this.localData))
            }
        },
        // 计算生肖总金额
        sxAmount(numArr, money) {
            let amount = 0
            this.tableData.forEach(item => {
                if (numArr.includes(item.name)) {
                    amount += Number(money)
                }
            })
            return amount
        },
        AmountByBS(color, money, status) {
            // 计算总金额
            this.amount += this.bsAmount(color, money)
            console.log('AmountByBS_amount', this.amount)

            let temp = []

            // 计算各个年龄段属于的生肖的金额
            this.tableData.forEach(item => {
                const colorMap = {
                    '红': 'red',
                    '蓝': 'blue',
                    '绿': 'green'
                }
                let colorTemp = color.map(item => colorMap[item])
                if (colorTemp.includes(item.color)) {
                    item.amount += Number(money)
                    temp.push(item.name)
                }
            })
            // 计算各个年龄段的百分比
            this.tableData.forEach(item => {
                item.percentage = parseFloat((item.amount / this.amount * 100).toFixed(2))
            })

            // 计算各个生肖的金额
            temp.forEach(item => {
                const index = this.zodiacsData.findIndex(z => z.name === item)
                this.zodiacsData[index].amount += Number(money)
            })
            // 计算各个生肖的百分比
            this.zodiacsData.forEach(item => {
                item.percentage = parseFloat((item.amount / this.amount * 100).toFixed(2))
            })
            
            // 将数据存储到本地
            if (status) {
                const obj = {
                    num: color.join(','),
                    money: money,
                    amount: this.bsAmount(color, money),
                    date: this.$moment().format('YYYY-MM-DD HH:mm:ss'),
                    type: 'bs'
                }
                this.localData.push(obj)
                localStorage.setItem('localData', JSON.stringify(this.localData))
            }
        },
        // 计算波色金额
        bsAmount(color, money) {
            let amount = 0
            const colorMap = {
                    '红': 'red',
                    '蓝': 'blue',
                    '绿': 'green'
                }
            let colorTemp = color.map(item => colorMap[item])
            colorTemp.forEach(item => {
                this.tableData.forEach(i => {
                    if (i.color === item) {
                        amount += Number(money)
                    }
                })
            })
            return amount
        },
        calculateCol18Height() {
            const col18Element = document.getElementById('col18');
            if (col18Element) {
                this.col18Height = col18Element.clientHeight
            }
        },
        handleDelete(row) {
            if (row.type === 'num') {
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
            } else if (row.type === 'sx') {
                const index = this.localData.findIndex(item => item.num === row.num && item.money === row.money)
                this.localData.splice(index, 1)
                this.amount -= row.amount
                const numArr = row.num.split(',')

                let temp = []
                this.tableData.forEach(item => {
                    if (numArr.includes(item.name)) {
                        item.amount -= Number(row.money)
                        temp.push(item.name)
                    }
                })
                this.tableData.forEach(item => {
                    if (this.amount === 0) {
                        item.percentage = 0
                    } else {
                        item.percentage = parseFloat((item.amount / this.amount * 100).toFixed(2))
                    }
                })


                temp.forEach(item => {
                    const index = this.zodiacsData.findIndex(z => z.name === item)
                    this.zodiacsData[index].amount -= Number(row.money)
                })
                this.zodiacsData.forEach(item => {
                    if (this.amount === 0) {
                        item.percentage = 0
                    } else {
                        item.percentage = parseFloat((item.amount / this.amount * 100).toFixed(2))
                    }
                })

            } else if (row.type === 'bs') {
                const index = this.localData.findIndex(item => item.num === row.num && item.money === row.money)
                this.localData.splice(index, 1)
                this.amount -= row.amount
                const color = row.num.split(',')
                let temp = []
                const colorMap = {
                    '红': 'red',
                    '蓝': 'blue',
                    '绿': 'green'
                }
                let colorTemp = color.map(item => colorMap[item])
                colorTemp.forEach(item => {
                    this.tableData.forEach(i => {
                        if (i.color === item) {
                            i.amount -= Number(row.money)
                            temp.push(i.name)
                        }
                    })
                })
                this.tableData.forEach(item => {
                    if (this.amount === 0) {
                        item.percentage = 0
                    } else {
                        item.percentage = parseFloat((item.amount / this.amount * 100).toFixed(2))
                    }
                })
                temp.forEach(item => {
                    const index = this.zodiacsData.findIndex(z => z.name === item)
                    this.zodiacsData[index].amount -= Number(row.money)
                })
                this.zodiacsData.forEach(item => {
                    if (this.amount === 0) {
                        item.percentage = 0
                    } else {
                        item.percentage = parseFloat((item.amount / this.amount * 100).toFixed(2))
                    }
                })
            }
            console.log('handleDelete', row)
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
        },
        changeHM(val) {
            this.checkHM = val
            this.form.num = val.join(',')
        },
        changeSX(val) {
            this.checkSX = val
            this.formSX.name = val.join(',')
        },
        sortTable() {
            let arr = []
            this.tableData.forEach(item => {
                arr.push(item)
            })
            arr.sort((a, b) => {
                return b.amount - a.amount
            })
            // 金额为0的去掉
            arr = arr.filter(item => item.amount !== 0)
            this.amountTable = arr
            console.log('sortTable', arr)
        }
    }
}
</script>
<style>
.num_box {
    display: inline-block;
    margin: 5px;
    text-align: center;
}
.first_box {
    writing-mode: vertical-lr;
    height: 670px;
}
.first_box_item {
    width: 15%;
    display: inline-block;
    writing-mode: horizontal-tb; 
}
.icon_1 {
    padding: 10px;
    margin: 10px;
    color: #fff;
    font-size: 20px;
    border-radius: 50%;
    width: 25px;
    height: 25px;
    display: flex;
    align-items: center;
    justify-content: center;
}
.icon_2 {
    padding: 10px;
    margin: 10px;
    color: #fff;
    font-size: 18px;
    border-radius: 50%;
    width: 15px;
    height: 15px;
    display: flex;
    align-items: center;
    justify-content: center;
}
.el-checkbox-button__inner {
    padding: 0 10px 0 0;
    width: 100%;
}
</style>