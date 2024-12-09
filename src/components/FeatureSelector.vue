<template>
  <div>
    <button
      v-if="FeatureOptional[featureKey]"
      :class="{
        'feature-button': true,
        'empty-variant': true,
        'selected-feature': isSelected(undefined),
      }"
      @click="emit('setVariant', featureKey, undefined)"
    ></button>
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
        height="150"
        width="150"
        viewBox="0 0 1024 1024"
        fill="none"
        xmlns="http://www.w3.org/2000/svg"
      >
        <FeatureGenerator
          :feature-key="featureKey"
          :variant="variant"
          :color="featureData.color"
        />
      </svg>
    </button>
  </div>
</template>

<script lang="ts" setup>
import { computed } from 'vue'
import {
  type FeatureBaseProps,
  FeatureGenerator,
  FeatureInfo,
  FeatureOptional,
  type FeatureType,
} from 'vue-adventurer'

const props = defineProps<{
  featureData: FeatureBaseProps
  featureKey: FeatureType
}>()

const emit = defineEmits<{
  (e: 'setVariant', featureKey: FeatureType, variant?: string): void
}>()

const variantKeys = computed(() => {
  const featureKey = props.featureKey
  const featureVariants = FeatureInfo[featureKey]?.variants
  if (!featureKey || !featureVariants) {
    return []
  }
  return Object.keys(featureVariants)
})

const selectedVariant = computed(() => props.featureData.variant)

const isSelected = (variant?: string) => {
  return variant === selectedVariant.value
}
</script>

<style lang="less" scoped>
.feature-button {
  margin: 0.5rem;
  border: 1px solid var(--p-gray-300);
  border-radius: 0.5rem;
  cursor: pointer;
  background-color: var(--p-gray-100);
  &:hover {
    border: 3px solid var(--p-primary-color);
    background-color: var(--p-gray-300);
  }
  &.selected-feature {
    border: 3px solid var(--p-primary-color);
  }
}
.empty-variant {
  width: 150px;
  height: 150px;
}
</style>
