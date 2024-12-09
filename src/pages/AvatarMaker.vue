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
          />
        </TabPanel>
      </TabPanels>
    </Tabs>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import { Adventurer, FeatureKey } from 'vue-adventurer'
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
    variant: 'hair-2',
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
  [FeatureKey.glasses]: {
    variant: 'glasses-1',
    color: undefined,
  },
  [FeatureKey.earrings]: {
    variant: 'earring-1',
    color: undefined,
  },
  [FeatureKey.marking]: {
    variant: 'marking-1',
    color: undefined,
  },
} as AdventurerProps)

const setVariant = (featureKey: FeatureType, variant?: string) => {
  if (!variant) {
    selectedFeatures.value[featureKey] = undefined
    return
  }

  // variant has value
  if (!selectedFeatures.value[featureKey]) {
    selectedFeatures.value[featureKey] = {}
  }

  if (selectedFeatures.value[featureKey]) {
    selectedFeatures.value[featureKey].variant = variant
  }
}
</script>

<style lang="less" scoped>
@import '@/styles/spacing.less';
@import '@/styles/colors.less';

@banner-height: 300px;
@banner-width: 100%;

.page-container {
  height: 100vh;
  background-color: var(--p-content-background);

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
}
</style>
