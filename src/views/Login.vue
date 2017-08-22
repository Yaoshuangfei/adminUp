<template>
  <el-form :model="ruleForm2" :rules="rules2" ref="ruleForm2" label-position="left" label-width="0px" class="demo-ruleForm login-container">
    <h3 class="title">商家管理中心</h3>
    <el-form-item prop="account">
      <el-input type="text" v-model="ruleForm2.username" auto-complete="off" @keyup.enter.native="keyLogin" placeholder="账号"></el-input>
    </el-form-item>
    <el-form-item prop="checkPass">
      <el-input type="password" v-model="ruleForm2.password" auto-complete="off" @keyup.enter.native="keyLogin" placeholder="密码"></el-input>
    </el-form-item>
    <!--<el-form-item >-->
      <!--<el-col :span='8' prop="yanzm">-->
        <!--<el-input v-model="ruleForm2.code" auto-complete="off" placeholder="验证码"></el-input>-->
      <!--</el-col>-->
      <!--<el-col :span='12'><img :src='imgsrc' @click="getuid"></el-col>-->
    <!--</el-form-item>-->
    <!-- <el-checkbox v-model="checked" checked class="remember">记住密码</el-checkbox> -->
    <el-form-item style="width:100%;">
      <el-button type="primary" style="width:100%;" @click.native.prevent="logi"  :loading="logining">登录</el-button>
    </el-form-item>
  </el-form>
</template>

<script>
    import { requestLogin ,loginID ,baseUrl } from '../api/api';
    import { state } from '../vuex/state'
    //import NProgress from 'nprogress'
    export default {
        data() {
            return {
                uuid: '',
                imgsrc: '',
                logining: false,
                ruleForm2: {
                    username: '',
                    password: '',
                    code:''
                },
                rules2: {
                    username: [
                        { required: true, message: '请输入账号', trigger: 'blur' },
                        //{ validator: validaePass }
                    ],
                    password: [
                        { required: true, message: '请输入密码', trigger: 'blur' },
                        //{ validator: validaePass2 }
                    ],
                    yanzm: [
                        { required: true, message: '请输入验证码', trigger: 'blur' },
                        //{ validator: validaePass2 }
                    ]
                },
                checked: true
            };
        },
        computed: {
            
        },
        methods: {
            keyLogin(ev){
             if (ev.keyCode==13){
                 this.logi()
             }
            },
            logi() {
                const _this = this
                // var url  = 'http://192.168.0.106:8080/api/core/partnersLogin';
                var url  = baseUrl+'/api/core/partnersLogin';
                var data = {userName: this.ruleForm2.username, password: this.ruleForm2.password};
                $.ajax({
                    type:'POST',
                    dataType:'json',
                    url:url,
                    data:JSON.stringify(data),
                    contentType:'application/json;charset=utf-8',
                    error: function (XMLHttpRequest, textStatus, errorThrown) {},
                    success:function(data){
                        if(!data.success){
                            alert(data.msg);
                        }else{
                            // console.log(data)
                            state.storeId = data.data.storeId
                            state.id = data.data.id
                            state.storeStatus = data.data.storeStatus
                            document.cookie="token="+data.data.token;
                            sessionStorage.setItem('user', JSON.stringify(_this.ruleForm2.username));
                            _this.$router.push({ path: '/main' });
                            _this.getcommissionLine()
                        }
                    }
                });
            },
            getcommissionLine(){
                $.ajax({
                    type:'GET',
                    dataType:'json',
                    url:baseUrl+'/api/store/selectStore',
                    // data:JSON.stringify(data),
                    contentType:'application/json;charset=utf-8',
                    success:function(data){
                        // console.log(data)
                        state.commissionLine = data.data.store.commissionLine
                    }
                });
            }
        },
        mounted() {
            // this.getcommissionLine()
        },
    }

</script>

<style lang="scss" scoped>
  .login-container {
    /*box-shadow: 0 0px 8px 0 rgba(0, 0, 0, 0.06), 0 1px 0px 0 rgba(0, 0, 0, 0.02);*/
    -webkit-border-radius: 5px;
    border-radius: 5px;
    -moz-border-radius: 5px;
    background-clip: padding-box;
    margin: 180px auto;
    margin-right: 300px;
    margin-top: 320px;
    width: 350px;
    padding: 35px 35px 15px 35px;
    background: #fff;
    border: 1px solid #eaeaea;
    box-shadow: 0 0 25px #cac6c6;
    .title {
      margin: 0px auto 40px auto;
      text-align: center;
      color: #505458;
    }
    .remember {
      margin: 0px 0px 35px 0px;
    }
    img{
      cursor:pointer;
    }
  }
</style>