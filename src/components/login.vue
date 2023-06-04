
<template>
    <div v-if="!qrCodeVisible">
        <!--帐户登陆的内容-->
        <div class="row">
            <el-input v-model="username" placeholder="用户名" prefix-icon="el-icon-user" clearable="clearable" />
        </div>
        <div class="row">
            <el-input type="password" v-model="password" placeholder="密码" prefix-icon="el-icon-lock"
                clearable="clearable" />
        </div>
        <div class="row">
            <el-button type="primary" class="btn" @click="login">登陆系统</el-button>
        </div>
        <div class="row"><a class="link" @click="changeMode">二维码登陆</a></div>
    </div>
    <div v-if="qrCodeVisible">
        <!--二维码登陆的内容-->
    </div>
</template>
<script>
import { isUsername, isPassword } from '../utils/validate'

export default {
    data: function () {
        return {
            username: null,
            password: null,
            qrCodeVisible: false,
            qrCode: '',
            uuid: null,
            qrCodeTimer: null,
            loginTimer: null
        };
    },
    login: function() {
    let that = this;
    if (!isUsername(that.username)) {
        that.$message({
            message: '用户名格式不正确',
            type: 'error',
            duration: 1200
        });
    } else if (!isPassword(that.password)) {
        that.$message({
            message: '密码格式不正确',
            type: 'error',
            duration: 1200
        });
    } else {
        let data = { username: that.username, password: that.password };
        //发送登陆请求
        that.$http('user/login', 'POST', data, true, function(resp) {
            if (resp.result) {
                //在浏览器的storage中存储用户权限列表，这样其他页面也可使用storage中的数据，实现共享
                let permissions = resp.permissions;
                //取出Token令牌，保存到storage中
                let token = resp.token;
                localStorage.setItem('permissions', permissions);
                localStorage.setItem('token', token);
                //让路由跳转页面，这里的Home是home.vue页面的名字
                router.push({ name: 'Home' });
            } else {
                that.$message({
                    message: '登陆失败',
                    type: 'error',
                    duration: 1200
                });
            }
        });
    }
},

}

</script>