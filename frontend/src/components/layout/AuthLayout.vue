<template>
  <div class="auth-page flex min-h-screen items-center justify-center overflow-y-auto px-4 py-10 sm:px-6">
    <main class="auth-content-shell w-full max-w-[420px]">
      <header class="mb-7 flex flex-col items-center text-center sm:mb-8">
        <template v-if="settingsLoaded">
          <div
            class="auth-brand-logo inline-flex h-16 w-16 shrink-0 items-center justify-center overflow-hidden bg-white"
          >
            <img :src="siteLogo || '/logo.svg'" alt="Logo" class="h-full w-full object-contain" />
          </div>
          <h1 class="mt-3 text-2xl font-semibold text-gray-900 dark:text-gray-100">
            {{ siteName }}
          </h1>
          <p class="mt-1 max-w-sm text-[13px] leading-5 text-gray-500 dark:text-gray-400">
            {{ siteSubtitle }}
          </p>
        </template>
      </header>

      <section class="auth-card-shell p-6 sm:p-7">
        <slot />
      </section>

      <div class="mt-5 text-center text-sm text-gray-500 dark:text-gray-400">
        <slot name="footer" />
      </div>

      <div class="mt-8 text-center text-xs text-gray-500 dark:text-gray-400">
        &copy; {{ currentYear }} {{ siteName }}. All rights reserved.
      </div>
    </main>
  </div>
</template>

<script setup lang="ts">
import { computed, onMounted } from 'vue'
import { useAppStore } from '@/stores'
import { sanitizeUrl } from '@/utils/url'

const appStore = useAppStore()

const siteName = computed(() => appStore.siteName || 'Sub2API')
const siteLogo = computed(() => sanitizeUrl(appStore.siteLogo || '', { allowRelative: true, allowDataUrl: true }))
const siteSubtitle = computed(() => appStore.cachedPublicSettings?.site_subtitle || 'Subscription to API Conversion Platform')
const settingsLoaded = computed(() => appStore.publicSettingsLoaded)

const currentYear = computed(() => new Date().getFullYear())

onMounted(() => {
  appStore.fetchPublicSettings()
})
</script>
