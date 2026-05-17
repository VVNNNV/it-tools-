# IT-TOOLS 暗夜模式系统指南

## 概述

IT-TOOLS 已集成完整的暗夜模式（Dark Mode）切换系统。用户可以轻松在亮色和暗色主题之间切换，系统会自动保存用户的主题偏好。

## 系统架构

### 核心组件

#### 1. 样式存储 (`src/stores/style.store.ts`)

管理应用的样式状态，包括主题切换和菜单折叠状态。

```typescript
export const useStyleStore = defineStore('style', {
  state: () => {
    const isDarkTheme = useDark();           // 检测系统主题
    const toggleDark = useToggle(isDarkTheme); // 切换函数
    const isSmallScreen = useMediaQuery('(max-width: 700px)');
    const isMenuCollapsed = useStorage('isMenuCollapsed', isSmallScreen.value);
    
    return {
      isDarkTheme,
      toggleDark,
      isMenuCollapsed,
      isSmallScreen,
    };
  },
});
```

#### 2. 主题配置 (`src/themes.ts`)

定义亮色和暗色主题的 UI 组件样式覆盖。

```typescript
// 亮色主题
export const lightThemeOverrides: GlobalThemeOverrides = {
  Layout: { color: '#f1f5f9' },
  // ... 其他组件配置
};

// 暗色主题
export const darkThemeOverrides: GlobalThemeOverrides = {
  common: {
    primaryColor: '#1ea54cFF',
    // ... 其他配置
  },
  Layout: {
    color: '#1c1c1c',
    siderColor: '#232323',
  },
  // ... 其他组件配置
};
```

#### 3. 主应用 (`src/App.vue`)

根据 `isDarkTheme` 状态动态应用主题。

```vue
<script setup>
const theme = computed(() => (styleStore.isDarkTheme ? darkTheme : null));
const themeOverrides = computed(() => 
  (styleStore.isDarkTheme ? darkThemeOverrides : lightThemeOverrides)
);
</script>

<template>
  <n-config-provider :theme="theme" :theme-overrides="themeOverrides">
    <!-- 应用内容 -->
  </n-config-provider>
</template>
```

#### 4. 导航栏按钮 (`src/components/NavbarButtons.vue`)

提供主题切换按钮，显示太阳/月亮图标。

```vue
<c-button circle variant="text" @click="() => styleStore.toggleDark()">
  <n-icon v-if="isDarkTheme" size="25" :component="IconSun" />
  <n-icon v-else size="25" :component="IconMoon" />
</c-button>
```

#### 5. 新增主题切换组件 (`src/components/ThemeToggle.vue`)

增强的主题切换组件，包含动画效果。

```vue
<script setup>
const toggleTheme = () => {
  styleStore.toggleDark();
};
</script>

<template>
  <c-button @click="toggleTheme">
    <transition name="theme-fade" mode="out-in">
      <n-icon v-if="isDarkTheme" key="sun" size="25" :component="IconSun" />
      <n-icon v-else key="moon" size="25" :component="IconMoon" />
    </transition>
  </c-button>
</template>
```

## 使用方法

### 在组件中检测当前主题

```vue
<script setup>
import { useStyleStore } from '@/stores/style.store';
import { storeToRefs } from 'pinia';

const styleStore = useStyleStore();
const { isDarkTheme } = storeToRefs(styleStore);
</script>

<template>
  <div :class="{ 'dark-mode': isDarkTheme }">
    <p v-if="isDarkTheme">当前为暗色模式</p>
    <p v-else>当前为亮色模式</p>
  </div>
</template>
```

### 切换主题

```vue
<script setup>
import { useStyleStore } from '@/stores/style.store';

const styleStore = useStyleStore();

const handleThemeToggle = () => {
  styleStore.toggleDark();
};
</script>

<template>
  <button @click="handleThemeToggle">切换主题</button>
</template>
```

## 主题自适应 Logo

项目已集成主题自适应 Logo 系统，会根据当前主题自动切换 Logo。

### Logo 文件

- **亮色模式**: `src/assets/logo-light.png`
- **暗色模式**: `src/assets/logo-dark.png`

### 实现方式

```vue
<script setup>
import LogoLight from '../assets/logo-light.png';
import LogoDark from '../assets/logo-dark.png';
import { useStyleStore } from '@/stores/style.store';
import { storeToRefs } from 'pinia';

const styleStore = useStyleStore();
const { isDarkTheme } = storeToRefs(styleStore);
const currentLogo = computed(() => isDarkTheme.value ? LogoDark : LogoLight);
</script>

<template>
  <img :src="currentLogo" alt="IT-TOOLS Logo" class="logo-image" />
</template>
```

## 自定义主题

### 修改主题颜色

编辑 `src/themes.ts` 文件：

```typescript
export const darkThemeOverrides: GlobalThemeOverrides = {
  common: {
    primaryColor: '#1ea54cFF',        // 主色
    primaryColorHover: '#36AD6AFF',   // 悬停色
    primaryColorPressed: '#0C7A43FF', // 按下色
  },
  Layout: {
    color: '#1c1c1c',      // 背景色
    siderColor: '#232323', // 侧边栏色
  },
};
```

### 添加新的主题变量

```typescript
export const customThemeOverrides: GlobalThemeOverrides = {
  common: {
    primaryColor: '#your-color',
    errorColor: '#ff0000',
    warningColor: '#ffaa00',
    successColor: '#00ff00',
    infoColor: '#0000ff',
  },
};
```

## CSS 主题适配

### 使用 CSS 变量

```css
:root {
  --primary-color: #5cb0ff;
  --background-color: #ffffff;
  --text-color: #000000;
}

:root.dark {
  --primary-color: #5cb0ff;
  --background-color: #1c1c1c;
  --text-color: #ffffff;
}

.my-component {
  color: var(--text-color);
  background-color: var(--background-color);
}
```

### 使用 LESS 媒体查询

```less
.my-component {
  color: #000;
  background: #fff;

  @media (prefers-color-scheme: dark) {
    color: #fff;
    background: #1c1c1c;
  }
}
```

### 使用 Vue 条件类

```vue
<template>
  <div :class="{ 'dark-mode': isDarkTheme }">
    <p>内容</p>
  </div>
</template>

<style scoped>
.dark-mode {
  background-color: #1c1c1c;
  color: #ffffff;
}
</style>
```

## 主题持久化

系统使用 `@vueuse/core` 的 `useDark()` 函数，会自动：

1. **检测系统主题偏好**: 通过 `prefers-color-scheme` 媒体查询
2. **保存用户选择**: 在 localStorage 中存储用户的主题偏好
3. **恢复用户偏好**: 页面加载时自动应用保存的主题

```typescript
const isDarkTheme = useDark({
  attribute: 'class',
  valueDark: 'dark',
  valueLight: 'light',
  storageKey: 'vueuse-color-scheme',
  onChanged(isDark: boolean) {
    // 主题改变时的回调
  },
});
```

## 广告在暗夜模式下的适配

广告组件已自动适配暗夜模式：

```vue
<script setup>
import { useStyleStore } from '@/stores/style.store';
import { storeToRefs } from 'pinia';

const styleStore = useStyleStore();
const { isDarkTheme } = storeToRefs(styleStore);

// 根据主题选择不同的广告代码
const adCode = computed(() => {
  if (isDarkTheme.value) {
    return darkModeAdCode;
  } else {
    return lightModeAdCode;
  }
});
</script>

<template>
  <AdPlaceholder placement="header" :ad-code="adCode" />
</template>
```

## 过渡动画

### 主题切换动画

```vue
<style scoped>
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
</style>
```

### 全局过渡

```vue
<template>
  <transition name="theme-switch" mode="out-in">
    <div :key="isDarkTheme" class="theme-content">
      <!-- 内容 -->
    </div>
  </transition>
</template>

<style>
.theme-switch-enter-active,
.theme-switch-leave-active {
  transition: opacity 0.3s ease;
}

.theme-switch-enter-from,
.theme-switch-leave-to {
  opacity: 0;
}
</style>
```

## 最佳实践

### 1. 使用主题变量而非硬编码颜色

❌ **不推荐**:
```vue
<style scoped>
.my-component {
  color: #000;
  background: #fff;
}
</style>
```

✅ **推荐**:
```vue
<script setup>
import { useThemeVars } from 'naive-ui';

const themeVars = useThemeVars();
</script>

<template>
  <div :style="{ color: themeVars.textColor1 }">
    内容
  </div>
</template>
```

### 2. 为暗夜模式提供适当的对比度

- 文字与背景的对比度至少为 4.5:1
- 使用浅色文字在深色背景上
- 避免纯黑色背景，使用深灰色 (#1c1c1c)

### 3. 测试暗夜模式

```bash
# 在浏览器开发者工具中测试
# 1. 打开 DevTools
# 2. 按 Ctrl+Shift+P (Windows) 或 Cmd+Shift+P (Mac)
# 3. 输入 "Rendering" 并选择 "Show Rendering"
# 4. 在 "Emulate CSS media feature prefers-color-scheme" 中选择 "dark"
```

### 4. 提供用户控制

确保用户可以轻松切换主题：
- 在导航栏放置主题切换按钮
- 提供键盘快捷键（可选）
- 记住用户的主题偏好

## 常见问题

### Q: 如何强制使用某个主题？
**A**: 
```typescript
const isDarkTheme = useDark({
  disrespectUserPreference: true,
  valueDark: 'dark',
  valueLight: 'light',
});
```

### Q: 如何在特定组件中禁用主题切换？
**A**: 
```vue
<script setup>
const isDarkTheme = ref(false); // 固定为亮色
</script>
```

### Q: 如何添加更多主题选项（如蓝色主题）？
**A**: 
```typescript
export const blueThemeOverrides: GlobalThemeOverrides = {
  common: {
    primaryColor: '#0066cc',
    // ... 其他配置
  },
};

// 在 App.vue 中
const theme = computed(() => {
  if (styleStore.isDarkTheme) return darkTheme;
  if (styleStore.isBlueTheme) return blueTheme;
  return null;
});
```

## 相关资源

- [Naive UI 主题文档](https://www.naiveui.com/zh-CN/dark)
- [Vue 3 组合式 API](https://vuejs.org/guide/extras/composition-api-faq.html)
- [VueUse 文档](https://vueuse.org/)
- [CSS 媒体查询](https://developer.mozilla.org/en-US/docs/Web/CSS/Media_Queries)

## 更新日志

### v1.0 (2024)
- ✅ 集成 Naive UI 主题系统
- ✅ 实现自动主题检测和切换
- ✅ 添加主题自适应 Logo
- ✅ 创建 ThemeToggle 增强组件
- ✅ 支持主题持久化存储

---

**最后更新**: 2024 年  
**版本**: 1.0
