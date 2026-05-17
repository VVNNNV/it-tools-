# IT-TOOLS 广告集成指南

## 概述

本指南说明如何在 IT-TOOLS 项目中集成广告代码。项目已为您准备了多个广告放置位置和相关组件。

## 项目结构

```
src/
├── components/
│   ├── AdPlaceholder.vue          # 广告占位符组件
│   ├── ThemeToggle.vue            # 暗夜模式切换组件（新增）
│   └── NavbarButtons.vue          # 导航栏按钮（已集成主题切换）
├── layouts/
│   ├── base.layout.vue            # 基础布局（已集成新 Logo）
│   └── tool.layout.vue            # 工具布局
├── assets/
│   ├── logo-light.png             # 亮色模式 Logo（新增）
│   ├── logo-dark.png              # 暗色模式 Logo（新增）
│   └── hero-gradient.svg           # 原始渐变背景
└── pages/                         # 页面文件
```

## 广告放置位置

### 1. 页头广告 (Header Advertisement)

**位置**: 应用顶部导航栏下方  
**文件**: `src/layouts/base.layout.vue`  
**推荐尺寸**: 728x90px (Leaderboard) 或 970x90px (Large Leaderboard)  
**适用场景**: 品牌广告、推广活动

**集成代码示例**:
```vue
<template #content>
  <!-- 页头广告 -->
  <AdPlaceholder placement="header" :ad-code="headerAdCode" />
  
  <!-- 其他内容 -->
</template>
```

### 2. 侧边栏广告 (Sidebar Advertisement)

**位置**: 左侧菜单栏下方  
**文件**: `src/layouts/base.layout.vue`  
**推荐尺寸**: 300x250px (Medium Rectangle) 或 160x600px (Wide Skyscraper)  
**适用场景**: 产品推荐、相关服务

**集成代码示例**:
```vue
<template #sider>
  <!-- 菜单内容 -->
  <CollapsibleToolMenu :tools-by-category="tools" />
  
  <!-- 侧边栏广告 -->
  <AdPlaceholder placement="sidebar" :ad-code="sidebarAdCode" />
</template>
```

### 3. 页脚广告 (Footer Advertisement)

**位置**: 页面底部  
**文件**: `src/layouts/base.layout.vue`  
**推荐尺寸**: 728x90px (Leaderboard)  
**适用场景**: 品牌推广、合作伙伴链接

**集成代码示例**:
```vue
<template #content>
  <!-- 页面内容 -->
  <slot />
  
  <!-- 页脚广告 -->
  <AdPlaceholder placement="footer" :ad-code="footerAdCode" />
</template>
```

### 4. 工具页内联广告 (Inline Advertisement)

**位置**: 工具页面内容区域  
**文件**: `src/layouts/tool.layout.vue` 或具体工具页面  
**推荐尺寸**: 300x250px (Medium Rectangle)  
**适用场景**: 相关工具推荐、赞助商链接

**集成代码示例**:
```vue
<template>
  <!-- 工具内容 -->
  <div class="tool-content">
    <!-- 工具主体 -->
  </div>
  
  <!-- 内联广告 -->
  <AdPlaceholder placement="inline" :ad-code="inlineAdCode" />
</template>
```

## 广告代码集成方法

### 方法 1: 直接在组件中使用 AdPlaceholder

```vue
<script setup>
import AdPlaceholder from '@/components/AdPlaceholder.vue';

// Google AdSense 代码示例
const headerAdCode = `
  <script async src="https://pagead2.googlesyndication.com/pagead/js/adsbygoogle.js?client=ca-pub-xxxxxxxxxxxxxxxx"
     crossorigin="anonymous"><\/script>
  <ins class="adsbygoogle"
     style="display:block"
     data-ad-client="ca-pub-xxxxxxxxxxxxxxxx"
     data-ad-slot="xxxxxxxxxx"
     data-ad-format="auto"
     data-full-width-responsive="true"><\/ins>
  <script>
     (adsbygoogle = window.adsbygoogle || []).push({});
  <\/script>
`;
</script>

<template>
  <AdPlaceholder placement="header" :ad-code="headerAdCode" />
</template>
```

### 方法 2: 使用环境变量配置

**文件**: `.env` 或 `.env.local`
```
VITE_HEADER_AD_CODE="<script>...</script>"
VITE_SIDEBAR_AD_CODE="<script>...</script>"
VITE_FOOTER_AD_CODE="<script>...</script>"
```

**在组件中使用**:
```vue
<script setup>
const headerAdCode = import.meta.env.VITE_HEADER_AD_CODE || '';
</script>

<template>
  <AdPlaceholder placement="header" :ad-code="headerAdCode" />
</template>
```

### 方法 3: 创建广告配置文件

**文件**: `src/config/ads.config.ts`
```typescript
export const adsConfig = {
  header: {
    enabled: true,
    code: `<script async src="..."><\/script>`,
    placeholder: '728x90',
  },
  sidebar: {
    enabled: true,
    code: `<script async src="..."><\/script>`,
    placeholder: '300x250',
  },
  footer: {
    enabled: true,
    code: `<script async src="..."><\/script>`,
    placeholder: '728x90',
  },
  inline: {
    enabled: true,
    code: `<script async src="..."><\/script>`,
    placeholder: '300x250',
  },
};
```

**在组件中使用**:
```vue
<script setup>
import { adsConfig } from '@/config/ads.config';
</script>

<template>
  <AdPlaceholder 
    v-if="adsConfig.header.enabled"
    placement="header" 
    :ad-code="adsConfig.header.code" 
  />
</template>
```

## 常见广告平台集成

### Google AdSense

1. 注册 [Google AdSense](https://www.google.com/adsense/)
2. 获取您的 Publisher ID (ca-pub-xxxxxxxxxxxxxxxx)
3. 创建广告单元并获取代码
4. 将代码集成到 AdPlaceholder 组件

**代码示例**:
```html
<script async src="https://pagead2.googlesyndication.com/pagead/js/adsbygoogle.js?client=ca-pub-xxxxxxxxxxxxxxxx"
   crossorigin="anonymous"></script>
<ins class="adsbygoogle"
   style="display:block"
   data-ad-client="ca-pub-xxxxxxxxxxxxxxxx"
   data-ad-slot="xxxxxxxxxx"
   data-ad-format="auto"
   data-full-width-responsive="true"></ins>
<script>
   (adsbygoogle = window.adsbygoogle || []).push({});
</script>
```

### 百度广告

1. 注册 [百度联盟](https://union.baidu.com/)
2. 创建广告位获取代码
3. 集成到 AdPlaceholder 组件

**代码示例**:
```html
<script>
(function(){
    var _53code = document.createElement("script");
    _53code.src = "http://p.53kf.com/article/getJs?v=1";
    document.head.appendChild(_53code);
})();
</script>
```

### 其他广告平台

- **Adsterra**: https://adsterra.com/
- **PropellerAds**: https://propellerads.com/
- **Monetag**: https://monetag.com/
- **Clickadu**: https://clickadu.com/

## 暗夜模式适配

项目已集成暗夜模式切换系统。广告组件会自动适配暗夜模式：

- **亮色模式**: 使用浅色背景和深色文字
- **暗色模式**: 使用深色背景和浅色文字

**自动检测当前主题**:
```vue
<script setup>
import { useStyleStore } from '@/stores/style.store';
import { storeToRefs } from 'pinia';

const styleStore = useStyleStore();
const { isDarkTheme } = storeToRefs(styleStore);
</script>

<template>
  <div :class="{ 'dark-mode': isDarkTheme }">
    <AdPlaceholder placement="header" />
  </div>
</template>
```

## 新增 Logo 系统

项目已集成响应式 Logo 系统，会根据主题自动切换：

- **亮色模式**: 使用 `logo-light.png`
- **暗色模式**: 使用 `logo-dark.png`

Logo 文件位置: `src/assets/`

### 更换 Logo

1. 准备新的 Logo 文件 (PNG 或 SVG 格式)
2. 放入 `src/assets/` 目录
3. 修改 `src/layouts/base.layout.vue` 中的导入语句

```vue
<script setup>
import LogoLight from '../assets/your-logo-light.png';
import LogoDark from '../assets/your-logo-dark.png';
</script>
```

## 性能优化建议

1. **延迟加载广告**: 使用 `v-if` 条件渲染，仅在需要时加载
2. **异步加载**: 将广告脚本设置为异步加载
3. **缓存**: 利用浏览器缓存减少重复加载
4. **响应式设计**: 确保广告在不同屏幕尺寸上正常显示

**延迟加载示例**:
```vue
<script setup>
import { ref } from 'vue';

const showAds = ref(false);

onMounted(() => {
  // 页面加载完成后 2 秒显示广告
  setTimeout(() => {
    showAds.value = true;
  }, 2000);
});
</script>

<template>
  <AdPlaceholder v-if="showAds" placement="header" />
</template>
```

## 测试广告集成

1. **本地测试**: 在开发环境中测试广告显示
2. **构建测试**: 运行 `npm run build` 进行生产构建测试
3. **跨浏览器测试**: 在不同浏览器中验证广告显示
4. **响应式测试**: 测试不同屏幕尺寸下的广告显示

## 常见问题

### Q: 广告代码不显示？
**A**: 检查以下几点：
- 确保广告代码正确无误
- 检查浏览器控制台是否有错误
- 确认广告平台账户状态正常
- 检查广告过滤器/插件是否阻止了广告

### Q: 暗夜模式下广告显示不正常？
**A**: 
- 在 AdPlaceholder 组件中添加主题检测
- 为不同主题提供不同的广告代码
- 使用 CSS 媒体查询适配暗夜模式

### Q: 如何禁用某个广告位？
**A**: 
```vue
<AdPlaceholder 
  v-if="adsConfig.header.enabled"
  placement="header" 
  :ad-code="adsConfig.header.code" 
/>
```

## 相关文件

- 广告占位符组件: `src/components/AdPlaceholder.vue`
- 暗夜切换组件: `src/components/ThemeToggle.vue`
- 基础布局: `src/layouts/base.layout.vue`
- 工具布局: `src/layouts/tool.layout.vue`
- 样式配置: `src/themes.ts`

## 支持

如有问题，请参考项目文档或提交 Issue。

---

**最后更新**: 2024 年  
**版本**: 1.0
