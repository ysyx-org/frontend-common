<script setup>
import { computed, ref, watch } from 'vue';
import { useRoute } from 'vue-router';
import { useNavStore } from '@CS/nav';
const props = defineProps({
    // Used for vue-router
    to: {
        type: String,
        default: '',
    },
    // Used for page navigation
    href: {
        type: String,
        default: '',
    },
    // Used for title name display
    title: {
        type: String,
        default: '',
    },
    disabled: Boolean,
    active: Boolean,
});
const nav = useNavStore();
const emit = defineEmits(['active']);
// Router watcher
const route = useRoute(),
    checkUrl = (path = route.path) =>
        isActive.value = !!props.to && (
            props.to === '/'
                ? path == '/'
                : path.startsWith(props.to)
        );
watch(
    () => route.path,
    checkUrl
);
const isActive = ref(false);
watch(isActive, active => {
    if (active) nav.activeTitle(props.title);
});
const disabled = computed(() => props.disabled || nav.redirecting);
checkUrl();
</script>

<template>
    <responsive>
        <tri
            role="nav-link"
            :class="[
                active || isActive ? 'active' : '',
                disabled ? 'disabled' : '',
            ]"
            :to="to"
            :href="href"
        >
            <slot></slot>
        </tri>
    </responsive>
</template>

<style lang="scss">
[role=nav-link] {
  /* Appearance */
  font-size: 0.92em;
  text-decoration: none;
  user-select: none;
  color: var(--ct-gray-dark);
  cursor: pointer;
  font-weight: 500;
  background: none !important;
  display: block;
  & > i {
    margin-right: 0.5em;
  }
  &:not(.active) {
    color: var(--ct-gray);
    i {
      opacity: 0.5;
    }
  }
  [collapse="false"] & {
    height: 100%;
    margin: 0 0.2em !important;
    padding: 2px 0.5em;
    display: flex;
    justify-content: center;
    align-items: center;
    line-height: calc(var(--header-height) - 4px);
    overflow: hidden;
    &::after {
      content: "";
      display: block;
      position: absolute;
      left: 0.3em;
      right: 0.3em;
      bottom: 0;
      height: 3px;
      background-color: transparent;
      transform: translateY(3px) scaleX(0);
      transition: 1s var(--curve);
    }
    &:not(.active):hover::after {
      background-color: var(--cb-gray);
      transform: translateY(0) scaleX(1);
    }
    &.active::after {
      background-color: var(--c-brand);
      transform: translateY(0) scaleX(1);
    }
  }
  [collapse="true"] & {
    margin: 0 !important;
    font-weight: bold;
    font-size: 16px;
    padding: 0.4em 1em;
    display: block;
    text-align: left;
    border-left: 8px solid transparent;
    line-height: calc(var(--header-height) - 4px);
    &.active {
      border-left: 8px solid var(--c-brand);
      background-color: var(--cf-next-level) !important;
    }
  }
  &.disabled {
    pointer-events: none;
    opacity: 0.8;
  }
}
</style>
