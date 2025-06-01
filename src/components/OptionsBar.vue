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
          @update:model-value="(val?: string) => onColorPicked(featureKey, val)"
        />
        <label for="color-text-input">color</label>
      </FloatLabel>
    </InputGroup>
    <Button
      label="DOWNLOAD"
      severity="secondary"
      :pt="ButtonStyle"
      @click="emit('downloadAdventurer')"
    />
    <Button
      icon="pi pi-user"
      severity="secondary"
      rounded
      aria-label="User"
      size="small"
      class="icon-button"
      @click="toggleAttributionPopover"
    />
    <Popover ref="attributionPopOver">
      <div class="popover-container">
        <span>
          Assets are designed by
          <a href="https://www.instagram.com/lischi_art/" target="_blank">
            Lisa Wischofsky
          </a>
          and was modified under
          <a
            href="https://creativecommons.org/licenses/by/4.0/"
            target="_blank"
          >
            CC BY 4.0
          </a>
        </span>
      </div>
    </Popover>
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
import Popover from 'primevue/popover'
import { FeatureInfo, type FeatureType } from 'vue-adventurer'

const props = defineProps<{
  currentColor?: string
  disabled?: boolean
  featureKey: FeatureType // determines the type of options displayed
}>()

const emit = defineEmits<{
  (e: 'setColor', featureKey: FeatureType, color?: string): void // for changing the color
  (e: 'downloadAdventurer'): void
  (e: 'showAttributionPopup'): void
}>()

const ButtonStyle = {
  label: {
    style: {
      'font-size': '0.75rem',
      padding: '0.5rem',
    },
  },
}

const colorSelectionTimeout = ref<Timeout | null>(null)
const attributionPopOver = ref(null)
const selectedColor = computed(
  () =>
    props.currentColor ??
    FeatureInfo[props.featureKey]?.defaultColor ??
    '#b0b0b0',
)
const isColorPickerDisabled = computed(
  () => !FeatureInfo[props.featureKey]?.defaultColor || props.disabled,
)

const onColorPicked = (featureKey: FeatureType, color?: string) => {
  // Cancel previous updates in the last 300ms
  // Because we are overwriting it with color
  clearTimeout(colorSelectionTimeout.value)
  // Update color after 300ms
  colorSelectionTimeout.value = setTimeout(() => {
    emit('setColor', featureKey, color)
  }, 200)
}

const toggleAttributionPopover = (event: Event) => {
  attributionPopOver.value?.toggle?.(event)
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
  align-items: center;

  label {
    font-size: 0.75rem;
  }

  .checkbox-gap {
    gap: 0.25rem;
  }

  .icon-button {
    width: 40px;
    height: 40px;
  }
}

.p-color-picker-overlay-custom {
  z-index: 2;
}

.popover-container {
  max-width: 300px;
  font-size: 1rem;
  line-height: 1.5rem;
}
</style>

<style lang="less">
.p-color-picker-overlay-custom {
  z-index: 100 !important;
}
</style>
