<template>
    <div>
        <el-menu
            class="sidebar-container-menu"
            mode="vertical"
            :default-active="activeMenu"
            :background-color="scssVariables.menuBg"
            :text-color="scssVariables.menuText"
            :active-text-color="scssVariables.menuActiveText"
            :collapse="isCollapse"
            :collapse-transition="true"
        >
            <sidebar-item
                v-for="route in menuRoutes"
                :key="route.path"
                :item="route"
                :base-path="route.path"
            />
        </el-menu>
    </div>
</template>

<script lang="ts">
import { defineComponent, computed, ref } from "vue";
import { useRoute } from "vue-router";
import variables from "@/style/variables.scss";
import { useStore } from "@/store";
// 导入路由表
import { routes } from "@/router";
import SidebarItem from "../SidebarItem/index.vue";

export default defineComponent({
    name: "Sidebar",
    components: {
        SidebarItem,
    },
    setup() {
        const store = useStore();
        const route = useRoute(); // 等价于 this.$route
        // 根据路由路径 对应 当前激活的菜单
        const activeMenu = computed(() => {
            const { path, meta } = route;
            if (meta.activeMenu) {
                return meta.activeMenu;
            }
            return path;
        });
        // scss变量
        const scssVariables = computed(() => {
            const cssVar: { [key: string]: string } = {};
            variables
                .replace(":export", "")
                .replace("{", "")
                .replace("}", "")
                .replace(";", "")
                .split("\n")
                .filter((item) => item.trim())
                .map((item) => {
                    const [key, value] = item.split(": ");
                    cssVar[key.trim()] = value;
                });
            return cssVar;
        });

        // 展开收起状态 稍后放store
        const isCollapse = computed(() => !store.getters.sidebar.opened);

        // 渲染路由
        const menuRoutes = computed(() => routes);

        return {
            // 不有toRefs原因 缺点在这里 variables里面变量属性感觉来源不明确 不知道有哪些变量值
            // ...toRefs(variables),
            scssVariables,
            isCollapse,
            activeMenu,
            menuRoutes,
        };
    },
});
</script>
