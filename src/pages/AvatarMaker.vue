<template>
  <div class="page-container">
    <Toast />
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
        ref="adventurerComponent"
      />
      <OptionsBar
        :feature-key="activeTab"
        :disabled="!selectedFeatures[activeTab]"
        :current-color="selectedFeatures[activeTab]?.color"
        @setColor="setColor"
        @download-adventurer="downloadSvg"
      />
    </div>

    <div class="feature-selector-area">
      <Tabs v-model:value="activeTab" scrollable>
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
import Toast from 'primevue/toast'
import OptionsBar from '@/components/OptionsBar.vue'
import { useToast } from 'primevue/usetoast'

const toast = useToast()

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

const activeTab = ref(FeatureKey.hair)

const adventurerComponent = ref(null)

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

  // check if color starts with #
  selectedFeatures.value[featureKey].color = color.startsWith('#')
    ? color
    : `#${color}`
}

const downloadSvg = () => {
  // Try to get the root DOM element of the component
  const rootEl = adventurerComponent.value?.$el ?? adventurerComponent.value
  // Query for the <svg> inside or check if rootEl is itself <svg>
  const svg =
    rootEl?.querySelector?.('svg') ??
    (rootEl instanceof SVGSVGElement ? rootEl : null)
  if (!svg) {
    toast.add({
      severity: 'error',
      summary: 'Failed to Download',
      detail: 'Could not find the adventurer svg',
      life: 3000,
    })
    return
  }

  const serializer = new XMLSerializer()
  const source = serializer.serializeToString(svg)

  const blob = new Blob([source], { type: 'image/svg+xml;charset=utf-8' })
  const url = URL.createObjectURL(blob)

  const link = document.createElement('a')
  link.href = url
  link.download = 'downloaded-adventurer.svg'
  document.body.appendChild(link)
  link.click()
  document.body.removeChild(link)

  URL.revokeObjectURL(url)
}
</script>

<style lang="less" scoped>
@import '@/styles/spacing.less';
@import '@/styles/colors.less';

@options-bar-height: 36px;
@banner-height: calc(max(25vh, 150px) + @options-bar-height);
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
  flex-direction: column;
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
