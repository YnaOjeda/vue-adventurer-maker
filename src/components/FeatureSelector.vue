<template>
  <div class="grid-container">
    <ColorPicker
      format="hex"
      :model-value="selectedColor"
      :disabled="!FeatureInfo[featureKey]?.defaultColor || !featureData"
      @update:model-value="val => onColorPicked(featureKey, val)"
    />
    <!-- Add a button for "no feature" option -->
    <button
      v-if="FeatureOptional[featureKey]"
      :class="{
        'feature-button': true,
        'selected-feature': isSelected(undefined),
      }"
      @click="emit('setVariant', featureKey, undefined)"
    >
      <svg
        height="100%"
        width="100%"
        viewBox="0 0 1024 1024"
        fill="none"
        xmlns="http://www.w3.org/2000/svg"
      />
    </button>
    <!-- Display the features -->
    <button
      v-for="variant in variantKeys"
      :class="{
        'feature-button': true,
        'selected-feature': isSelected(variant),
      }"
      :key="`selector-${variant}`"
      @click="emit('setVariant', featureKey, variant)"
    >
      <svg
        height="100%"
        width="100%"
        viewBox="0 0 1024 1024"
        fill="none"
        xmlns="http://www.w3.org/2000/svg"
      >
        <FeatureGenerator
          :feature-key="featureKey"
          :variant="variant"
          :color="featureData?.color"
        />
      </svg>
    </button>
  </div>
</template>

<script lang="ts" setup>
import { computed, ref } from 'vue'
import {
  type FeatureBaseProps,
  FeatureGenerator,
  FeatureInfo,
  FeatureOptional,
  type FeatureType,
} from 'vue-adventurer'

import { ColorPicker } from 'primevue'

const props = defineProps<{
  featureData?: FeatureBaseProps // determines which option is selected
  featureKey: FeatureType // determines the type of options displayed
}>()

const emit = defineEmits<{
  (e: 'setVariant', featureKey: FeatureType, variant?: string): void // for selecting a tile
  (e: 'setColor', featureKey: FeatureType, color?: string): void // for changing the color
  (e: 'update:modelValue', value: string): void
}>()

// Keys for the tiles to be displayed
const variantKeys = computed(() => {
  const featureKey = props.featureKey
  const featureVariants = FeatureInfo[featureKey]?.variants
  if (!featureKey || !featureVariants) {
    return []
  }
  return Object.keys(featureVariants)
})

// For debouncing too many color changes in a short amount of time
const colorSelectionTimeout = ref(null)

// Selected tile/option
const selectedVariant = computed(() => props.featureData?.variant)

// Selected color
const selectedColor = computed(
  () =>
    props.featureData?.color ?? // user-selected color
    FeatureInfo[props.featureKey]?.defaultColor ??
    '#b0b0b0',
)

// Determines which tile/option is selected
const isSelected = (variant?: string) => {
  if (!variant) {
    // variant chosen is none
    return !variant && !selectedVariant.value
  }
  return variant === selectedVariant.value
}

const onColorPicked = (featureKey: FeatureType, color: string) => {
  // Cancel previous updates in the last 300ms
  // Because we are overwriting it with color
  clearTimeout(colorSelectionTimeout.value)
  // Update color after 300ms
  colorSelectionTimeout.value = setTimeout(() => {
    emit('setColor', featureKey, color)
  }, 200)
}
</script>

<style lang="less" scoped>
@import '@/styles/spacing.less';

.grid-container {
  display: grid;
  padding-top: 0.5rem;
  gap: 1rem;
  position: relative;

  :deep(.p-colorpicker-preview) {
    width: 100%;
    height: 100%;
  }

  @media (max-width: @breakpoint-sm) {
    grid-template-columns: repeat(3, 1fr);
    padding-top: 0.75rem;
  }

  @media (min-width: @breakpoint-sm) and (max-width: @breakpoint-m) {
    grid-template-columns: repeat(4, 1fr);
  }

  @media (min-width: @breakpoint-m) and (max-width: @breakpoint-l) {
    grid-template-columns: repeat(5, 1fr);
  }

  @media (min-width: @breakpoint-l) {
    grid-template-columns: repeat(6, 1fr);
  }
}

.feature-button {
  border: 1px solid var(--p-gray-300);
  border-radius: 0.5rem;
  cursor: pointer;
  background-color: var(--p-gray-100);
  width: 100%;
  height: 100%;

  &:hover {
    border: 3px solid var(--p-primary-color);
    background-color: var(--p-gray-300);
  }
  &.selected-feature {
    border: 3px solid var(--p-primary-color);
  }
}
</style>
