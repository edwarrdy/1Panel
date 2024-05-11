<template>
    <div class="footer">
        <a href="https://fit2cloud.com/" target="_blank">Copyright © 青岛杰瑞工控技术有限公司</a>
<!--        <SystemUpgrade :footer="true" />-->
    </div>
</template>

<script setup lang="ts">
// import SystemUpgrade from '@/components/system-upgrade/index.vue';
</script>

<style scoped lang="scss">
.footer {
    display: flex;
    align-items: center;
    justify-content: space-between;
    height: 48px;
    background: #ffffff;
    border-top: 1px solid #e4e7ed;
    box-sizing: border-box;
    padding: 10px 20px;
    a {
        font-size: 12px;
        color: #858585;
        text-decoration: none;
        letter-spacing: 0.5px;
    }
    span {
        font-size: 12px;
        color: #858585;
        text-decoration: none;
        letter-spacing: 0.5px;
    }
}
</style>
