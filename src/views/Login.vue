<template>
    <div class="login-wrap">
        <div class="ms-login">
            <div class="ms-title">后台管理系统</div>
            <el-form :model="param" :rules="rules" ref="login" label-width="0px" class="ms-content">
                <el-form-item prop="username">
                    <el-input v-model="param.username" placeholder="username">
                        <template #prepend>
                            <el-button :icon="User"></el-button>
                        </template>
                    </el-input>
                </el-form-item>
                <el-form-item prop="password">
                    <el-input
                            type="password"
                            placeholder="password"
                            v-model="param.password"
                            @keyup.enter="submitForm(login)"
                    >
                        <template #prepend>
                            <el-button :icon="Lock"></el-button>
                        </template>
                    </el-input>
                </el-form-item>
                <div class="login-btn">
                    <el-button type="primary" @click="submitForm(login)">登录</el-button>
                </div>
            </el-form>
        </div>
    </div>
</template>

<script setup>
import {ref, reactive} from 'vue';
import {useAccountStore} from "@/stores/account.js";
import {useTagsStore} from '@/stores/tags.js';
import {useRouter} from 'vue-router';
import {ElMessage} from 'element-plus';
import {Lock, User} from '@element-plus/icons-vue';

const router = useRouter();
const param = reactive({
    username: 'admin',
    password: '123456'
});

const rules = {
    username: [
        {
            required: true,
            message: '请输入用户名',
            trigger: 'blur'
        }
    ],
    password: [{required: true, message: '请输入密码', trigger: 'blur'}]
};

const login = ref();
const submitForm = (formEl) => {
    if (!formEl) return;
    formEl.validate(async (valid) => {
        try {
            const message = await useAccountStore().adminLogin(param.username, param.password);
            if (valid && message === '登录成功') {
                ElMessage.success('登录成功');
                localStorage.setItem('ms_username', param.username);
                setTimeout(() => {
                    router.push('/');
                }, 1000);
            } else {
                ElMessage.error(message);
                return false;
            }
        } catch (e) {
            ElMessage.error(e);
        }
    });
};

const tags = useTagsStore();
tags.clearTags();
</script>

<style scoped>
.login-wrap {
    position: relative;
    width: 100%;
    height: 100%;
    background-image: url(../assets/img/login-bg.jpg);
    background-size: 100%;
}

.ms-title {
    width: 100%;
    line-height: 50px;
    text-align: center;
    font-size: 20px;
    color: #fff;
    border-bottom: 1px solid #ddd;
}

.ms-login {
    position: absolute;
    left: 50%;
    top: 50%;
    width: 350px;
    margin: -190px 0 0 -175px;
    border-radius: 5px;
    background: rgba(255, 255, 255, 0.3);
    overflow: hidden;
}

.ms-content {
    padding: 30px 30px;
}

.login-btn {
    text-align: center;
}

.login-btn button {
    width: 100%;
    height: 36px;
    margin-bottom: 10px;
}

.login-tips {
    font-size: 12px;
    line-height: 30px;
    color: #fff;
}
</style>
