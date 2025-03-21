<template>
  <NConfigProvider
    :key="key"
    :locale="generalLang"
    :date-locale="dateLang"
    :theme-overrides="themeOverrides"
  >
    <slot></slot>
  </NConfigProvider>
</template>

<script lang="ts" setup>
import { useLocalStorage } from "@vueuse/core";
import { NConfigProvider } from "naive-ui";
import { watch } from "vue";
import { useRoute } from "vue-router";
import { themeOverrides, dateLang, generalLang } from "../naive-ui.config";
import { provideAppRootContext } from "./AppRootContext";
import { useLanguage } from "./composables/useLanguage";
import { applyCustomTheme } from "./utils/customTheme";

const route = useRoute();
const { key } = provideAppRootContext();
const { setLocale } = useLanguage();
const cachedCustomTheme = useLocalStorage<string>("bb.custom-theme", "");

watch(
  () => route.fullPath,
  () => {
    const searchParams = new URLSearchParams(window.location.search);
    // Initial custom theme.
    const customTheme =
      searchParams.get("customTheme") || cachedCustomTheme.value;
    if (customTheme) {
      applyCustomTheme(customTheme);
      if (customTheme !== cachedCustomTheme.value) {
        // Save custom theme to local storage.
        cachedCustomTheme.value = customTheme;
      }
    }
    // Initial custom language.
    const lang = searchParams.get("lang") || "";
    if (lang) {
      setLocale(lang);
    }
  },
  {
    immediate: true,
  }
);
</script>
