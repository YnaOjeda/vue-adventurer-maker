<template>
  <div class="page-container">
    <div class="banner">
      <Adventurer
        :size="'100%'"
        :face="face"
        :marking="selectedFeatures.marking"
        :mouth="selectedFeatures.mouth"
        :eyes="selectedFeatures.eyes"
        :brows="selectedFeatures.brows"
        :glasses="selectedFeatures.glasses"
        :earrings="selectedFeatures.earrings"
        :hair="selectedFeatures.hair"
      />
    </div>

    <div class="feature-selector-area">
      <Tabs :value="FeatureTabs[0]" scrollable>
        <TabList>
          <Tab
            v-for="tab in FeatureTabs"
            :key="`feature-tab-${tab}`"
            :value="tab"
            >{{ tab }}</Tab
          >
        </TabList>
        <TabPanels>
          <TabPanel
            v-for="tab in FeatureTabs"
            :key="`feature-tab-panel-${tab}`"
            :value="tab"
          >
            <FeatureSelector
              :feature-key="tab"
              :feature-data="selectedFeatures[tab] as FeatureBaseProps"
              @set-variant="setVariant"
              @set-color="setColor"
            />
          </TabPanel>
        </TabPanels>
      </Tabs>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import { Adventurer, FeatureKey, FeatureOptional } from 'vue-adventurer'
import type {
  AdventurerProps,
  FeatureType,
  FeatureBaseProps,
} from 'vue-adventurer'
import { FeatureTabs } from '@/constants'
import Tabs from 'primevue/tabs'
import TabList from 'primevue/tablist'
import Tab from 'primevue/tab'
import TabPanels from 'primevue/tabpanels'
import TabPanel from 'primevue/tabpanel'
import FeatureSelector from '@/components/FeatureSelector.vue'

const face = ref<AdventurerProps['face']>({})

const selectedFeatures = ref({
  [FeatureKey.hair]: {
    variant: 'hair-1',
    color: undefined,
  },
  [FeatureKey.eyes]: {
    variant: 'eye-1',
    color: undefined,
  },
  [FeatureKey.brows]: {
    variant: 'brow-1',
    color: undefined,
  },
  [FeatureKey.mouth]: {
    variant: 'mouth-1',
    color: undefined,
  },
  [FeatureKey.glasses]: undefined,
  [FeatureKey.earrings]: undefined,
  [FeatureKey.marking]: undefined,
} as AdventurerProps)

const setVariant = (featureKey: FeatureType, variant?: string) => {
  if (!variant) {
    if (!FeatureOptional[featureKey]) {
      return // only allow undefined variants if feature is optional
    }

    selectedFeatures.value[featureKey] = undefined
    return
  }

  // variant has value, but check if feature object is defined
  if (!selectedFeatures.value[featureKey]) {
    selectedFeatures.value[featureKey] = {
      variant: undefined,
      color: undefined,
    }
  }

  selectedFeatures.value[featureKey].variant = variant
}

const setColor = (featureKey: FeatureType, color?: string) => {
  if (!color || !selectedFeatures.value[featureKey]) {
    return
  }

  selectedFeatures.value[featureKey].color = `#${color}`
}
</script>

<style lang="less" scoped>
@import '@/styles/spacing.less';
@import '@/styles/colors.less';

@banner-height: max(30vh, 150px);
@banner-width: 100%;
@menu-height: 48px;

.page-container {
  height: 100vh;
  background-color: var(--p-content-background);
  position: relative;
  overflow-y: hidden;

  @media (max-width: @breakpoint-m) {
    width: 100vw;
  }

  @media (min-width: @breakpoint-m) {
    width: 60vw;
    max-width: 1200px;
  }
}

.banner {
  width: 100%;
  height: @banner-height;
  justify-content: center;
  display: flex;
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  z-index: 10;
  background-color: var(--p-content-background);
}

.feature-selector-area {
  margin-top: @banner-height;
  position: relative;

  :deep(.p-tablist) {
    position: absolute;
    top: 0;
    left: 0;
    right: 0;
    z-index: 1;
  }

  :deep(.p-tabpanels) {
    margin-top: @menu-height;
    height: calc(100vh - @banner-height - @menu-height);
    overflow-y: scroll;
  }
}
</style>
