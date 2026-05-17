<script setup lang="ts">
import { IconBrandGithub, IconBrandX, IconInfoCircle, IconMoon, IconSun } from '@tabler/icons-vue';
import { useStyleStore } from '@/stores/style.store';

const styleStore = useStyleStore();
const { isDarkTheme } = toRefs(styleStore);

// 主题切换的动画效果
const toggleTheme = () => {
  styleStore.toggleDark();
};
</script>

<template>
  <div class="theme-toggle-container">
    <c-tooltip :tooltip="isDarkTheme ? $t('home.nav.lightMode') : $t('home.nav.darkMode')" position="bottom">
      <c-button 
        circle 
        variant="text" 
        :aria-label="$t('home.nav.mode')" 
        class="theme-toggle-btn"
        @click="toggleTheme"
      >
        <transition name="theme-fade" mode="out-in">
          <n-icon v-if="isDarkTheme" key="sun" size="25" :component="IconSun" />
          <n-icon v-else key="moon" size="25" :component="IconMoon" />
        </transition>
      </c-button>
    </c-tooltip>
  </div>
</template>

<style lang="less" scoped>
.theme-toggle-container {
  display: inline-flex;
  align-items: center;
}

.theme-toggle-btn {
  transition: all 0.3s ease;
  
  &:hover {
    transform: rotate(20deg);
  }
}

// 主题切换过渡动画
.theme-fade-enter-active,
.theme-fade-leave-active {
  transition: opacity 0.3s ease, transform 0.3s ease;
}

.theme-fade-enter-from {
  opacity: 0;
  transform: rotate(-180deg) scale(0.8);
}

.theme-fade-leave-to {
  opacity: 0;
  transform: rotate(180deg) scale(0.8);
}

.theme-fade-enter-to,
.theme-fade-leave-from {
  opacity: 1;
  transform: rotate(0) scale(1);
}
</style>
