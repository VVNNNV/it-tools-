<script setup lang="ts">
/**
 * 广告占位符组件
 * 用于在应用中放置广告代码
 * 
 * 使用方式：
 * <AdPlaceholder placement="header" />
 * <AdPlaceholder placement="sidebar" />
 * <AdPlaceholder placement="footer" />
 * <AdPlaceholder placement="inline" />
 */

interface Props {
  placement?: 'header' | 'sidebar' | 'footer' | 'inline';
  adCode?: string;
}

withDefaults(defineProps<Props>(), {
  placement: 'inline',
  adCode: '',
});
</script>

<template>
  <div :class="`ad-placeholder ad-${placement}`">
    <!-- 广告代码将在此处插入 -->
    <!-- 例如：Google AdSense, 百度广告, 其他广告联盟代码 -->
    <div v-if="adCode" v-html="adCode" />
    <div v-else class="ad-placeholder-empty">
      <!-- 占位符：替换为实际广告代码 -->
      <p>{{ placement }} Advertisement Slot</p>
    </div>
  </div>
</template>

<style lang="less" scoped>
.ad-placeholder {
  &.ad-header {
    width: 100%;
    padding: 10px 0;
    border-bottom: 1px solid rgba(0, 0, 0, 0.1);
    min-height: 90px;
  }

  &.ad-sidebar {
    width: 100%;
    padding: 15px 10px;
    margin: 15px 0;
    border-radius: 8px;
    background: rgba(0, 0, 0, 0.02);
    min-height: 250px;
  }

  &.ad-footer {
    width: 100%;
    padding: 15px 0;
    border-top: 1px solid rgba(0, 0, 0, 0.1);
    margin-top: 20px;
    min-height: 90px;
  }

  &.ad-inline {
    display: inline-block;
    padding: 10px;
    margin: 10px 0;
    min-height: 60px;
  }

  .ad-placeholder-empty {
    display: flex;
    align-items: center;
    justify-content: center;
    width: 100%;
    height: 100%;
    color: #999;
    font-size: 12px;
    background: rgba(0, 0, 0, 0.03);
    border-radius: 4px;
    border: 1px dashed #ddd;

    p {
      margin: 0;
    }
  }
}

// 暗夜模式适配
:deep(.dark) {
  .ad-placeholder {
    &.ad-header {
      border-bottom-color: rgba(255, 255, 255, 0.1);
    }

    &.ad-sidebar {
      background: rgba(255, 255, 255, 0.02);
    }

    &.ad-footer {
      border-top-color: rgba(255, 255, 255, 0.1);
    }

    .ad-placeholder-empty {
      background: rgba(255, 255, 255, 0.03);
      border-color: #444;
    }
  }
}
</style>
