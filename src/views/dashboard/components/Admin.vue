<template>
    <div class="app-container center">
        <el-empty description="欢迎来到 admin 角色专属首页" />
        <el-button @click="logout">跳转到首页</el-button>
    </div>
</template>
<script lang="ts" setup>
import { useUserStoreHook } from "@/store/modules/user.js"

function logout() {
    useUserStoreHook().logout()
    location.reload()
}
</script>
<style lang="scss" scoped>
.center {
    height: 100%;
    display: flex;
    justify-content: center;
    align-items: center;
}
</style>
