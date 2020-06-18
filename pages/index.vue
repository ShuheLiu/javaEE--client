<template>
    <div>
        <div style="position: fixed;width: 100%; height: 100%;left:0;top:0; z-index: -1; overflow: hidden">
            <img style="width: 100%;" src="../assets/background.jpg" alt="背景图片">
        </div>
        <el-tabs class="card" type="border-card">
            <el-tab-pane label="用户登录">
                <p class="title">
                    作业管理系统
                </p>
                <el-form :model="login_module" :rules="loginRule" label-width="100px" label-position="left">
                    <el-form-item label="学号/工号" prop="user_name">
                        <el-input v-model="login_module.user_name" style="width: 200px"></el-input>
                    </el-form-item>
                    <el-form-item label="密码" prop="user_pwd">
                        <el-input v-model="login_module.user_pwd" style="width: 200px" show-password></el-input>
                    </el-form-item>
                    <div style="margin-left: 100px;margin-bottom: 20px">
                        <el-radio v-model="type" label="1">老师</el-radio>
                        <el-radio v-model="type" label="2">学生</el-radio>
                    </div>
                </el-form>
                <div style="text-align: center">
                    <el-button type="primary" @click="submitForm" size="medium">登录</el-button>
                </div>
            </el-tab-pane>
            <el-tab-pane label="用户注册">
                <el-form :model="register_module" :rules="registerRule" label-width="100px" label-position="left">
                    <el-form-item label="姓名">
                        <el-input v-model="register_module.user_name" style="width: 200px"></el-input>
                    </el-form-item>
                    <el-form-item label="学号/工号">
                        <el-input v-model="register_module.user_id" style="width: 200px"></el-input>
                    </el-form-item>
                    <el-form-item label="密码">
                        <el-input v-model="register_module.user_pwd" type="password" style="width: 200px"></el-input>
                    </el-form-item>
                    <el-form-item label="二次确认密码" prop="confirmPsw">
                        <el-input v-model="register_module.user_confirm_pwd" type="password"
                                  style="width: 200px"></el-input>
                    </el-form-item>
                    <div style="margin-left: 100px;margin-bottom: 20px">
                        <el-radio v-model="type" label="1">老师</el-radio>
                        <el-radio v-model="type" label="2">学生</el-radio>
                    </div>
                </el-form>
                <div style="text-align: center">
                    <el-button @click="registerUser" type="primary" size="medium">注册</el-button>
                </div>
            </el-tab-pane>
        </el-tabs>
    </div>
</template>

<script>
    import Cookies from 'js-cookie'
    import API from "../api";
    import qs from "qs";

    export default {

        data() {
            let validateRepeatPassword = (rule, value, cb) => {
                console.log(value);
                if (value !== this.register_module.user_pwd) {
                    cb(new Error('两次输入密码不一致!'))
                } else {
                    cb()
                }
            };
            return {
                login_module: {
                    user_name: '',
                    user_pwd: '',
                },
                register_module: {
                    user_name: '',
                    user_id: '',
                    user_pwd: '',
                    user_confirm_pwd: ''
                },
                loginRule: {
                    user_name: [
                        {required: true, message: '请输入学号/工号', trigger: 'blur'}
                    ],
                    user_pwd: [
                        {required: true, message: '请输入密码', trigger: 'blur'}
                    ]
                },
                registerRule: {
                    registerPsw: [
                        {validate: validateRepeatPassword, trigger: 'blur'}
                    ]
                },
                type:'1',
            }
        },

        mounted() {

        },

        methods: {
            submitForm() {
                if(this.type==='1'){
                    let data = {
                        tid: parseInt(this.login_module.user_name),
                        tPassword: this.login_module.user_pwd
                    };
                    data = qs.stringify(data);
                    API.teacherLogin(data).then(res=>{
                        if(res==='Fail to login'){
                            alert("登录失败，请检查账号或者密码");
                            return;
                        }
                        Cookies.set("tid",this.login_module.user_name);
                        Cookies.set("name",res);
                        Cookies.set("type",'1');
                        this.$router.push({path: '/teacher'});
                    }).catch(msg=>{
                        alert(msg)
                    })
                }else{
                    let data = {
                        sid: parseInt(this.login_module.user_name),
                        sPassword: this.login_module.user_pwd
                    };
                    data = qs.stringify(data);
                    API.studentLogin(data).then(res=>{
                        if(res==='Login fail'){
                            alert("登录失败，请检查账号或者密码");
                            return;
                        }
                        Cookies.set("sid",this.login_module.user_name);
                        Cookies.set("name",res);
                        Cookies.set("type",'2');
                        this.$router.push({path: '/student'});
                    }).catch(msg=>{
                        alert(msg)
                    })
                }
            },
            registerUser: function () {
                if(this.type==='1'){
                    let data = {
                        tid:parseInt(this.register_module.user_id),
                        tName: this.register_module.user_name,
                        tPassword: this.register_module.user_pwd
                    };
                    data = qs.stringify(data);
                    API.teacherReg(data).then(res=>{
                        if(res==='Fail insert teacher'){
                            alert("注册失败，请检查是否已注册");
                            return;
                        }
                        alert("注册成功，请登录");
                    }).catch(msg=>{
                        alert(msg);
                    })
                }else{
                    let data = {
                        sid:parseInt(this.register_module.user_id),
                        sName: this.register_module.user_name,
                        sPassword: this.register_module.user_pwd
                    };
                    data = qs.stringify(data);
                    API.studentReg(data).then(res=>{
                        if(res==='Fail insert'){
                            alert("注册失败，请检查是否已注册");
                            return;
                        }
                        alert("注册成功，请登录");
                    }).catch(msg=>{
                        alert(msg);
                    })
                }

            }
        },

    }
</script>

<style scoped>
    .title {
        font-size: 1.8rem;
        text-align: center;
    }

    .card {
        width: 25rem;
        min-height: 15rem;
        margin: 10% auto;
        border-radius: 10px;
    }

</style>

