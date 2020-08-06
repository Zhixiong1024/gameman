<template>
    <div class="home">

        <el-container>
            <el-main>

                <el-card shadow="always" :body-style="{ padding: '10px' }">
                    <div class="userinfo">

                        <div class="left">
                            <el-avatar :src="imgUrl" shape="circle" fit="fill"></el-avatar>
                        </div>

                        <div class="right">
                            <div class="levName">{{lev[nowLev-1].name}}</div>
                            <el-progress :percentage="percentage" :text-inside="true" :stroke-width="16"
                                         class="userLev"></el-progress>
                        </div>

                    </div>
                </el-card>
                <div v-show="pageIndex==0">

                    <el-card shadow="always" class="todoList" v-show="todoList[0]">
                        <el-card shadow="hover" :body-style="{ padding: '13px' }" class="todoCard"
                                 v-for="(item,index) in todoList" :key="item.id">
                            <el-row class="todo">
                                <el-col :span="18">{{item.todo}}</el-col>
                                <el-col :span="6">
                                    <el-button size="mini" round @click="delTodo(item)">完成</el-button>
                                </el-col>
                            </el-row>
                        </el-card>
                    </el-card>
                </div>
                <div v-show="pageIndex==1">
                    <div class="block">
                        <el-timeline v-for="(item,index) in yesTodo" :key="item.id">
                            <el-timeline-item :timestamp="item.time" placement="top">
                                <el-card>
                                    <h4>{{item.todo}}</h4>
                                </el-card>
                            </el-timeline-item>

                        </el-timeline>
                    </div>
                </div>
                <div v-show="pageIndex==2" class="inputTodo">
                    <el-input
                            placeholder="请输入内容"
                            v-model="input"
                            clearable>
                    </el-input>
                    <el-button @click="addTodo" class="btnTodo">添加任务</el-button>
                </div>
            </el-main>
            <el-footer>
                <el-tabs tab-position="bottom" @tab-click="tabClick" stretch>
                    <el-tab-pane label="首页"></el-tab-pane>
                    <el-tab-pane label="历史列表"></el-tab-pane>
                    <el-tab-pane label="添加任务"></el-tab-pane>
                </el-tabs>
            </el-footer>
        </el-container>

    </div>
</template>

<script>

    export default {
        name: 'home',
        data() {
            return {
                pageIndex: 0,//页面
                imgUrl: "https://fuss10.elemecdn.com/e/5d/4a731a90594a4af544c0c25941171jpeg.jpeg",
                todoList: [],//未完成的列表
                input: '',//任务输入框
                yesTodo: [],//已完成的列表
                lev: [
                    {
                        level: 1,
                        name: '渣渣',
                        experience: 11,
                    },
                    {
                        level: 2,
                        name: '屌丝',
                        experience: 21,
                    },
                    {
                        level: 3,
                        name: '正常人',
                        experience: 42,
                    },
                    {
                        level: 4,
                        name: '大佬',
                        experience: 84,
                    },
                    {
                        level: 5,
                        name: '男神',
                        experience: 164,
                    },
                ],
                nowLev: 1,//当前等级
                experience: 10,//当前经验
                percentage: 0,//进度条百分比

            }
        },
        components: {},
        methods: {
            tabClick(val) {
                this.pageIndex = val.index
            },
            addTodo() {
                this.getStorage()

                if (!this.input) {
                    this.$message({
                        message: '请输入内容',
                        type: 'warning',
                        duration: 800
                    });
                    return
                }

                    let random = Math.ceil(Math.random() * 100);
                this.todoList.push({
                    id: random,
                    time: new Date().toLocaleString(),
                    todo: this.input
                })
                this.input = ''

                this.setStorage()

                this.$message({
                    message: '添加成功',
                    type: 'success',
                    duration: 800
                });
            },
            delTodo(val) {
                this.getStorage()
                this.yesTodo.push(val)
                this.todoList = this.todoList.filter((item) => {
                    return item.id != val.id
                })
                this.experience += 5
                this.getlev()

                this.setStorage()

                this.$message({
                    message: '恭喜你完成任务',
                    type: 'success',
                    duration: 800

                });
            },
            setStorage() {
                let todoListStorage = JSON.stringify(this.todoList);
                let yesTodoStorage = JSON.stringify(this.yesTodo);
                let experience = JSON.stringify(this.experience);
                let nowLev = JSON.stringify(this.nowLev);
                let percentage = JSON.stringify(this.percentage);
                window.localStorage.setItem("todoListStorage", todoListStorage);
                window.localStorage.setItem("yesTodoStorage", yesTodoStorage);
                window.localStorage.setItem("experience", experience);
                window.localStorage.setItem("nowLev", nowLev);
                window.localStorage.setItem("percentage", percentage);
            },
            getStorage() {
                if (window.localStorage.getItem("todoListStorage")) {
                    let getTodoListStorage = window.localStorage.getItem("todoListStorage");
                    this.todoList = JSON.parse(getTodoListStorage);
                }
                if (window.localStorage.getItem("experience")) {
                    let getExperience = window.localStorage.getItem("experience");
                    this.experience = JSON.parse(getExperience);
                }
                if (window.localStorage.getItem("nowLev")) {
                    let getNowLev = window.localStorage.getItem("nowLev");
                    this.nowLev = JSON.parse(getNowLev);
                }
                if (window.localStorage.getItem("percentage")) {
                    let percentage = window.localStorage.getItem("percentage");
                    this.percentage = JSON.parse(percentage);
                }

                if (window.localStorage.getItem("yesTodoStorage")) {
                    let getYesTodoStorage = window.localStorage.getItem("yesTodoStorage");
                    this.yesTodo = JSON.parse(getYesTodoStorage);
                }
            },
            getlev() {
                console.log(this.lev[this.nowLev].experience)
                if (this.experience >= this.lev[this.nowLev].experience) {
                    this.nowLev += 1
                }
                let temp = (this.experience / this.lev[this.nowLev].experience) * 100;
                this.percentage = parseInt(temp.toFixed(0));
            }
        },
        created() {
            this.getStorage();
        }
    }
</script>

<style scoped lang="scss">
    .el-main {
        height: 88vh;
    }

    .el-footer {
        /*height: 15vh;*/

    }

    .el-card {
        width: 100%;
    }


    .userinfo {
        display: flex;

        .right {
            margin-left: 30px;

            .levName {
                margin-left: 10px;
                text-align: left;
                color: #999;
            }
        }
    }

    .userLev {
        width: 200px;
    }

    .todoList {
        margin-top: 20px;

        .todo {
            display: flex;
            justify-content: center;
            align-items: center;
            height: 50px;

            .right {
                margin-left: 20%;
            }
        }
    }

    .todoCard {
        margin-top: 2px;
    }

    .el-timeline {
        margin-top: 10px;
    }

    .inputTodo {
        margin-top: 10px;

    }

    .btnTodo {
        margin-top: 10px;
    }
</style>

