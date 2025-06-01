<template>
  <div class="options-bar-container">
    <!-- Color Picker -->
    <InputGroup>
      <InputGroupAddon>
        <ColorPicker
          format="hex"
          :model-value="selectedColor"
          overlay-class="p-color-picker-overlay-custom "
          :disabled="isColorPickerDisabled"
          @update:model-value="val => onColorPicked(featureKey, val)"
        />
      </InputGroupAddon>
      <FloatLabel variant="in">
        <InputText
          id="color-text-input"
          :model-value="selectedColor"
          :disabled="isColorPickerDisabled"
          @update:model-value="val => onColorPicked(featureKey, val)"
        />
        <label for="color-text-input">color</label>
      </FloatLabel>
    </InputGroup>
    <!-- TODO: Download button -->
    <Button label="DOWNLOAD" severity="secondary" :pt="ButtonStyle" />
  </div>
</template>

<script setup lang="ts">
import { computed, ref } from 'vue'
import InputGroup from 'primevue/inputgroup'
import InputGroupAddon from 'primevue/inputgroupaddon'
import InputText from 'primevue/inputtext'
import ColorPicker from 'primevue/colorpicker'
import FloatLabel from 'primevue/floatlabel'
import Button from 'primevue/button'
import { FeatureInfo, type FeatureType } from 'vue-adventurer'

const props = defineProps<{
  currentColor?: string
  disabled?: boolean
  featureKey: FeatureType // determines the type of options displayed
}>()

const emit = defineEmits<{
  (e: 'setColor', featureKey: FeatureType, color?: string): void // for changing the color
}>()

const ButtonStyle = {
  label: {
    style: {
      'font-size': '0.75rem',
      padding: '0.5rem',
    },
  },
}

const ColorPickerPT = {
  root: {
    style: {
      'z-Index': 10,
    },
  },
}

const selectedColor = computed(
  () =>
    props.currentColor ??
    FeatureInfo[props.featureKey]?.defaultColor ??
    '#b0b0b0',
)
const colorSelectionTimeout = ref<Timeout | null>(null)
const isColorPickerDisabled = computed(
  () => !FeatureInfo[props.featureKey]?.defaultColor || props.disabled,
)

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

<style scoped lang="less">
@label-size: 0.75rem;

.options-bar-container {
  height: 48px;
  width: 100%;
  display: flex;
  padding: 0 1.125rem;
  gap: 1rem;

  label {
    font-size: 0.75rem;
  }

  .checkbox-gap {
    gap: 0.25rem;
  }
}

.p-color-picker-overlay-custom {
  z-index: 2;
}
</style>

<style lang="less">
.p-color-picker-overlay-custom {
  z-index: 100 !important;
}
</style>
