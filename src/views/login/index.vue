<script lang="ts" setup>
import { reactive, ref } from "vue"
import { useRouter } from "vue-router"
import { useUserStore } from "@/store/modules/user"
import { type FormInstance, type FormRules } from "element-plus"
import { User, Lock } from "@element-plus/icons-vue"
import { type LoginRequestData } from "@/api/login/types/login"
import { useFocus } from "./hooks/useFocus"

const router = useRouter()
const { handleBlur, handleFocus } = useFocus()

/** 登录表单元素的引用 */
const loginFormRef = ref<FormInstance | null>(null)

/** 登录按钮 Loading */
const loading = ref(false)
/** 登录表单数据 */
const loginFormData: LoginRequestData = reactive({
    username: "admin",
    password: "admin123"
})
/** 登录表单校验规则 */
const loginFormRules: FormRules = {
    username: [{ required: true, message: "请输入用户名", trigger: "blur" }],
    password: [
        { required: true, message: "请输入密码", trigger: "blur" },
        { min: 8, max: 16, message: "长度在 8 到 16 个字符", trigger: "blur" }
    ]
}
/** 登录逻辑 */
const handleLogin = () => {
    loginFormRef.value?.validate((valid: boolean, fields) => {
        if (valid) {
            loading.value = true
            useUserStore()
                .login(loginFormData)
                .then(() => {
                    router.push({ path: "/" })
                })
                .catch(() => {
                    loginFormData.password = ""
                })
                .finally(() => {
                    loading.value = false
                })
        } else {
            console.error("表单校验不通过", fields)
        }
    })
}
</script>

<template>
    <div class="login-container">
        <div class="login-card">
            <div class="title">
                <span>欢迎使用AIGC</span>
            </div>
            <div class="content">
                <el-form ref="loginFormRef" :model="loginFormData" :rules="loginFormRules" @keyup.enter="handleLogin">
                    <el-form-item prop="username">
                        <el-input
                            v-model.trim="loginFormData.username"
                            placeholder="用户名"
                            type="text"
                            tabindex="1"
                            :prefix-icon="User"
                            size="large"
                        />
                    </el-form-item>
                    <el-form-item prop="password">
                        <el-input
                            v-model.trim="loginFormData.password"
                            placeholder="密码"
                            type="password"
                            tabindex="2"
                            :prefix-icon="Lock"
                            size="large"
                            show-password
                            @blur="handleBlur"
                            @focus="handleFocus"
                        />
                    </el-form-item>
                    <el-button :loading="loading" type="primary" size="large" @click.prevent="handleLogin"
                        >登 录</el-button
                    >
                </el-form>
            </div>
        </div>
    </div>
</template>

<style lang="scss" scoped>
.login-container {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    width: 100%;
    min-height: 100%;
    background-image: url("@/assets/login/background.png");
    background-repeat: no-repeat;
    background-position: center;
    background-size: cover;
    .login-card {
        margin-left: 40%;
        width: 560px;
        max-width: 90%;
        border-radius: 20px;
        box-shadow: 0 0 10px #dcdfe6;
        background-color: var(--el-bg-color);
        overflow: hidden;
        .title {
            display: flex;
            justify-content: center;
            align-items: center;
            height: 144px;
            background-image: url("@/assets/login/login-title.png");
            background-repeat: no-repeat;
            background-position: center;
            background-size: cover;
            span {
                height: 28px;
                font-size: 28px;
                font-family:
                    Alibaba PuHuiTi 2,
                    Alibaba PuHuiTi 2-600;
                font-weight: 600;
                text-align: LEFT;
                color: #ffffff;
                line-height: 28px;
            }
            img {
                height: 100%;
            }
        }
        .content {
            padding: 26px 100px 66px 100px;
            .el-form-item {
                margin-bottom: 24px;
            }
            :deep(.el-input__wrapper) {
                width: 360px;
                height: 54px;
            }
            .el-button {
                width: 360px;
                height: 52px;
                background: linear-gradient(270deg, #5865f2 0%, #197cff);
                border-radius: 8px;
                box-shadow: 0px 10px 20px 0px rgba(26, 58, 171, 0.3);
            }
        }
    }
}
</style>
