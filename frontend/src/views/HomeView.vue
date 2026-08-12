<template>
  <!-- Custom Home Content: Full Page Mode -->
  <div v-if="hasHomeContent" class="min-h-screen">
    <iframe v-if="isHomeContentUrl" :src="homeContent.trim()" class="h-screen w-full border-0" allowfullscreen />
    <div v-else v-html="homeContent"></div>
  </div>

  <!-- Preserve the upstream compact-home setting. -->
  <div
    v-else-if="compactHomeEnabled"
    data-testid="compact-home"
    class="flex min-h-screen flex-col bg-gray-50 text-gray-900 dark:bg-dark-950 dark:text-white"
  >
    <header class="border-b border-gray-200 px-4 py-4 sm:px-6 dark:border-dark-800">
      <nav class="mx-auto flex max-w-5xl flex-wrap items-center justify-between gap-3 sm:gap-4">
        <router-link to="/" class="flex min-w-0 flex-1 items-center gap-3">
          <img :src="siteLogo || '/logo.svg'" alt="Logo" class="h-9 w-9 shrink-0 rounded-lg object-contain" />
          <span class="min-w-0 truncate text-base font-semibold">{{ siteName }}</span>
        </router-link>
        <div class="flex max-w-full shrink-0 flex-wrap items-center justify-end gap-2">
          <LocaleSwitcher />
          <button class="public-icon-button" type="button" :title="isDark ? t('home.switchToLight') : t('home.switchToDark')" @click="toggleTheme">
            <Icon v-if="isDark" name="sun" size="sm" />
            <Icon v-else name="moon" size="sm" />
          </button>
          <router-link :to="isAuthenticated ? dashboardPath : '/login'" class="public-login-link">
            {{ isAuthenticated ? t('home.dashboard') : t('home.login') }}
          </router-link>
        </div>
      </nav>
    </header>
    <main class="flex min-w-0 flex-1 items-center justify-center px-4 py-16 sm:px-6">
      <div class="min-w-0 max-w-2xl text-center">
        <img :src="siteLogo || '/logo.svg'" alt="Logo" class="mx-auto mb-6 h-20 w-20 rounded-2xl object-contain" />
        <h1 class="[overflow-wrap:anywhere] text-3xl font-bold md:text-4xl">{{ siteName }}</h1>
        <p class="mt-4 whitespace-pre-wrap [overflow-wrap:anywhere] text-base text-gray-600 dark:text-dark-300">{{ siteSubtitle }}</p>
        <router-link :to="isAuthenticated ? dashboardPath : '/login'" class="btn btn-primary mt-8">
          {{ isAuthenticated ? t('home.goToDashboard') : t('home.login') }}
        </router-link>
      </div>
    </main>
    <footer class="border-t border-gray-200 px-4 py-5 text-center text-sm text-gray-500 dark:border-dark-800 dark:text-dark-400">
      &copy; {{ currentYear }} {{ siteName }}
    </footer>
  </div>

  <!-- Fenno public site -->
  <div v-else class="fenno-home">
    <header class="public-header">
      <nav class="container public-nav">
        <router-link to="/" class="public-brand" aria-label="Home">
          <img v-if="siteLogo" :src="siteLogo" :alt="siteName" class="public-logo" />
          <span v-else class="public-logo-fallback">{{ siteName.charAt(0) }}</span>
          <span>{{ siteName }}</span>
        </router-link>
        <div class="public-nav-actions">
          <a v-if="docUrl" :href="docUrl" target="_blank" rel="noopener noreferrer" class="public-nav-link">
            <Icon name="book" size="sm" />
            <span>{{ t('home.docs') }}</span>
          </a>
          <LocaleSwitcher />
          <button class="public-icon-button" type="button" :title="isDark ? t('home.switchToLight') : t('home.switchToDark')" @click="toggleTheme">
            <Icon v-if="isDark" name="sun" size="sm" />
            <Icon v-else name="moon" size="sm" />
          </button>
          <router-link v-if="isAuthenticated" :to="dashboardPath" class="public-account-link">
            <span class="public-avatar">{{ userInitial }}</span>
            <span>{{ t('home.dashboard') }}</span>
            <Icon name="arrowRight" size="xs" />
          </router-link>
          <router-link v-else to="/login" class="public-login-link">{{ t('home.login') }}</router-link>
        </div>
      </nav>
    </header>

    <main>
      <section class="hero">
        <div class="hero-glow"></div>
        <div class="container hero-container">
          <div class="hero-kicker"><span class="status-dot"></span>{{ t('home.tags.subscriptionToApi') }}</div>
          <h1>
            <span>{{ siteName }}</span>
            <strong>{{ t('home.heroSubtitle') }}</strong>
          </h1>
          <p class="hero-desc">{{ siteSubtitle || t('home.heroDescription') }}</p>
          <div class="hero-actions">
            <router-link :to="isAuthenticated ? dashboardPath : '/login'" class="btn btn-primary hero-cta">
              {{ isAuthenticated ? t('home.goToDashboard') : t('home.getStarted') }}
              <Icon name="arrowRight" size="sm" />
            </router-link>
            <a v-if="docUrl" :href="docUrl" target="_blank" rel="noopener noreferrer" class="btn btn-secondary hero-secondary">
              {{ t('home.viewDocs') }}
            </a>
          </div>
          <div class="hero-badges">
            <span><Icon name="checkCircle" size="xs" />{{ t('home.tags.stickySession') }}</span>
            <span><Icon name="checkCircle" size="xs" />{{ t('home.tags.realtimeBilling') }}</span>
            <span><Icon name="checkCircle" size="xs" />OpenAI compatible</span>
          </div>
        </div>
      </section>

      <section class="model-marquee" aria-label="Supported models">
        <div class="marquee-track">
          <span v-for="model in marqueeModels" :key="`a-${model}`" class="model-pill">{{ model }}</span>
          <span v-for="model in marqueeModels" :key="`b-${model}`" class="model-pill">{{ model }}</span>
        </div>
      </section>

      <section class="tools-wall">
        <div class="container">
          <div class="section-heading">
            <span class="eyebrow">{{ t('home.providers.description') }}</span>
            <h2>{{ t('home.providers.title') }}</h2>
            <p>{{ t('home.heroDescription') }}</p>
          </div>
          <div class="tools-grid">
            <div v-for="tool in tools" :key="tool.name" class="tool-card">
              <span class="tool-mark" :class="tool.tone">{{ tool.mark }}</span>
              <span>{{ tool.name }}</span>
            </div>
          </div>
        </div>
      </section>

      <section class="setup-section">
        <div class="container">
          <div class="section-heading">
            <span class="eyebrow">{{ t('home.tags.subscriptionToApi') }}</span>
            <h2>{{ t('home.solutions.title') }}</h2>
            <p>{{ t('home.solutions.subtitle') }}</p>
          </div>
          <div class="setup-grid">
            <article v-for="(step, index) in setupSteps" :key="step.title" class="setup-card">
              <span class="step-number">0{{ index + 1 }}</span>
              <div class="step-icon"><Icon :name="step.icon" size="md" /></div>
              <h3>{{ step.title }}</h3>
              <p>{{ step.description }}</p>
            </article>
          </div>
        </div>
      </section>

      <section class="config-section">
        <div class="container config-layout">
          <div class="config-copy">
            <span class="eyebrow">{{ t('home.tags.stickySession') }}</span>
            <h2>{{ t('home.features.unifiedGateway') }}</h2>
            <p>{{ t('home.features.unifiedGatewayDesc') }}</p>
            <ul>
              <li v-for="feature in configFeatures" :key="feature"><Icon name="check" size="sm" />{{ feature }}</li>
            </ul>
          </div>
          <div class="config-demo">
            <div class="config-tabs" role="tablist">
              <button v-for="item in configTabs" :key="item.id" type="button" :class="{ active: activeConfig === item.id }" @click="activeConfig = item.id">
                {{ item.label }}
              </button>
            </div>
            <div class="code-window">
              <div class="code-window-bar"><span></span><span></span><span></span><b>{{ activeConfig }}.config</b></div>
              <pre><code>{{ activeConfigText }}</code></pre>
            </div>
          </div>
        </div>
      </section>

      <section class="feature-strip">
        <div class="container feature-grid">
          <article v-for="feature in featureCards" :key="feature.title" class="feature-card">
            <div class="feature-icon" :class="feature.tone"><Icon :name="feature.icon" size="md" /></div>
            <h3>{{ feature.title }}</h3>
            <p>{{ feature.description }}</p>
          </article>
        </div>
      </section>

      <section class="cta-section">
        <div class="container">
          <div class="cta-card">
            <span class="eyebrow">{{ t('home.tags.realtimeBilling') }}</span>
            <h2>{{ t('home.cta.title') }}</h2>
            <p>{{ t('home.cta.description') }}</p>
            <router-link :to="isAuthenticated ? dashboardPath : '/login'" class="btn cta-button">
              {{ isAuthenticated ? t('home.goToDashboard') : t('home.cta.button') }}
              <Icon name="arrowRight" size="sm" />
            </router-link>
          </div>
        </div>
      </section>
    </main>

    <footer class="public-footer">
      <div class="container public-footer-inner">
        <p>&copy; {{ currentYear }} {{ siteName }}. {{ t('home.footer.allRightsReserved') }}</p>
        <div class="public-footer-links">
          <a v-if="docUrl" :href="docUrl" target="_blank" rel="noopener noreferrer">{{ t('home.docs') }}</a>
        </div>
      </div>
    </footer>
  </div>
</template>

<script setup lang="ts">
import { computed, onMounted, ref } from 'vue'
import { useI18n } from 'vue-i18n'
import { useAuthStore, useAppStore } from '@/stores'
import LocaleSwitcher from '@/components/common/LocaleSwitcher.vue'
import Icon from '@/components/icons/Icon.vue'
import { sanitizeUrl } from '@/utils/url'

const { t } = useI18n()
const authStore = useAuthStore()
const appStore = useAppStore()

const siteName = computed(() => appStore.cachedPublicSettings?.site_name || appStore.siteName || 'Sub2API')
const siteLogo = computed(() => sanitizeUrl(appStore.cachedPublicSettings?.site_logo || appStore.siteLogo || '', { allowRelative: true, allowDataUrl: true }))
const siteSubtitle = computed(() => appStore.cachedPublicSettings?.site_subtitle || t('home.heroDescription'))
const docUrl = computed(() => sanitizeUrl(appStore.cachedPublicSettings?.doc_url || appStore.docUrl || ''))
const homeContent = computed(() => appStore.cachedPublicSettings?.home_content || '')
const hasHomeContent = computed(() => homeContent.value.trim().length > 0)
const compactHomeEnabled = computed(() => appStore.cachedPublicSettings?.compact_home_enabled === true)
const isHomeContentUrl = computed(() => /^https?:\/\//i.test(homeContent.value.trim()))
const isDark = ref(document.documentElement.classList.contains('dark'))
const isAuthenticated = computed(() => authStore.isAuthenticated)
const isAdmin = computed(() => authStore.isAdmin)
const dashboardPath = computed(() => (isAdmin.value ? '/admin/dashboard' : '/dashboard'))
const userInitial = computed(() => authStore.user?.email?.charAt(0).toUpperCase() || '')
const currentYear = computed(() => new Date().getFullYear())

const marqueeModels = ['gpt-5.5', 'gpt-5.4', 'claude-sonnet-4-6', 'glm-5.2', 'gemini-2.5-pro']
const tools = [
  { name: 'Codex CLI', mark: 'C', tone: 'blue' },
  { name: 'Codex Desktop', mark: 'C', tone: 'blue' },
  { name: 'Claude Code', mark: 'A', tone: 'orange' },
  { name: 'Cline', mark: 'CL', tone: 'violet' },
  { name: 'Roo Code', mark: 'R', tone: 'rose' },
  { name: 'OpenCode', mark: 'O', tone: 'slate' },
  { name: 'Trae', mark: 'T', tone: 'cyan' },
  { name: 'Cursor', mark: 'Cu', tone: 'indigo' },
  { name: 'Windsurf', mark: 'W', tone: 'teal' },
  { name: 'CCSwitch', mark: 'CC', tone: 'green' }
]

const setupSteps = computed(() => [
  { title: t('home.features.unifiedGateway'), description: t('home.features.unifiedGatewayDesc'), icon: 'userPlus' as const },
  { title: t('home.features.multiAccount'), description: t('home.features.multiAccountDesc'), icon: 'key' as const },
  { title: t('home.features.balanceQuota'), description: t('home.features.balanceQuotaDesc'), icon: 'terminal' as const }
])

const featureCards = computed(() => [
  { title: t('home.features.unifiedGateway'), description: t('home.features.unifiedGatewayDesc'), icon: 'server' as const, tone: 'blue' },
  { title: t('home.features.multiAccount'), description: t('home.features.multiAccountDesc'), icon: 'users' as const, tone: 'violet' },
  { title: t('home.features.balanceQuota'), description: t('home.features.balanceQuotaDesc'), icon: 'chartBar' as const, tone: 'green' }
])

const configFeatures = computed(() => [t('home.tags.subscriptionToApi'), t('home.tags.stickySession'), t('home.tags.realtimeBilling')])
const configTabs = [
  { id: 'codex', label: 'Codex' },
  { id: 'claude', label: 'Claude Code' },
  { id: 'opencode', label: 'OpenCode' }
] as const
const activeConfig = ref<(typeof configTabs)[number]['id']>('codex')
const activeConfigText = computed(() => {
  const baseUrl = appStore.apiBaseUrl || window.location.origin
  if (activeConfig.value === 'claude') {
    return `ANTHROPIC_BASE_URL=${baseUrl}\nANTHROPIC_API_KEY=YOUR_API_KEY\n\n# Start Claude Code\nclaude`
  }
  if (activeConfig.value === 'opencode') {
    return `{"provider":"${siteName.value}","baseURL":"${baseUrl}/v1","apiKey":"YOUR_API_KEY"}`
  }
  return `model_provider = "${siteName.value}"\nmodel = "gpt-5.4"\n\n[model_providers.${siteName.value}]\nbase_url = "${baseUrl}/v1"\nwire_api = "responses"\n\n# ~/.codex/auth.json\n{ "OPENAI_API_KEY": "YOUR_API_KEY" }`
})

function toggleTheme() {
  isDark.value = !isDark.value
  document.documentElement.classList.toggle('dark', isDark.value)
  localStorage.setItem('theme', isDark.value ? 'dark' : 'light')
}

function initTheme() {
  const savedTheme = localStorage.getItem('theme')
  if (savedTheme === 'dark' || (!savedTheme && window.matchMedia('(prefers-color-scheme: dark)').matches)) {
    isDark.value = true
    document.documentElement.classList.add('dark')
  }
}

onMounted(() => {
  initTheme()
  authStore.checkAuth()
  if (!appStore.publicSettingsLoaded) appStore.fetchPublicSettings()
})
</script>

<style scoped>
.fenno-home { min-height: 100vh; overflow-x: hidden; background: #f9fafb; color: #111827; line-height: 1.5; }
.container { width: 100%; max-width: 1280px; margin: 0 auto; padding: 0 1.5rem; }
.public-header { position: absolute; inset: 0 0 auto; z-index: 10; border-bottom: 1px solid rgba(229,231,235,.8); background: rgba(249,250,251,.78); backdrop-filter: blur(18px); }
.public-nav { display: flex; min-height: 4.25rem; align-items: center; justify-content: space-between; gap: 1rem; }
.public-brand, .public-nav-actions, .public-nav-link, .public-account-link, .public-footer-inner, .public-footer-links { display: flex; align-items: center; }
.public-brand { gap: .65rem; color: #111827; font-size: .95rem; font-weight: 700; text-decoration: none; }
.public-logo, .public-logo-fallback { width: 1.9rem; height: 1.9rem; border-radius: .55rem; object-fit: contain; }
.public-logo-fallback { display: grid; place-items: center; background: #1c4fd8; color: white; font-size: .85rem; }
.public-nav-actions { gap: .45rem; }
.public-nav-link, .public-account-link, .public-login-link { color: #4b5563; font-size: .8rem; text-decoration: none; }
.public-nav-link { gap: .35rem; padding: .5rem .65rem; }
.public-nav-link:hover, .public-account-link:hover { color: #1c4fd8; }
.public-icon-button { display: grid; width: 2rem; height: 2rem; place-items: center; border: 0; border-radius: .45rem; background: transparent; color: #6b7280; cursor: pointer; }
.public-icon-button:hover { background: #eef4ff; color: #1c4fd8; }
.public-account-link { gap: .45rem; margin-left: .25rem; padding: .25rem .55rem .25rem .25rem; border: 1px solid #e5e7eb; border-radius: 999px; background: #fff; }
.public-avatar { display: grid; width: 1.7rem; height: 1.7rem; place-items: center; border-radius: 50%; background: #1c4fd8; color: #fff; font-size: .7rem; font-weight: 700; }
.public-login-link { padding: .52rem .85rem; border-radius: 999px; background: #111827; color: #fff; font-weight: 600; }
.hero { position: relative; display: flex; min-height: 43rem; align-items: center; justify-content: center; overflow: hidden; padding: 8rem 0 5.5rem; }
.hero-glow { position: absolute; top: 7rem; left: 50%; width: 46rem; height: 22rem; transform: translateX(-50%); border-radius: 50%; background: rgba(28,79,216,.1); filter: blur(80px); }
.hero-container { position: relative; z-index: 1; text-align: center; }
.hero-kicker, .hero-badges span, .eyebrow { display: inline-flex; align-items: center; gap: .4rem; color: #1c4fd8; font-size: .72rem; font-weight: 700; letter-spacing: .08em; text-transform: uppercase; }
.status-dot { width: .42rem; height: .42rem; border-radius: 50%; background: #1c4fd8; box-shadow: 0 0 0 4px #dce8ff; }
.hero h1 { max-width: 58rem; margin: 1rem auto 0; color: #111827; font-size: clamp(2.35rem, 7vw, 4.7rem); font-weight: 800; letter-spacing: -.045em; line-height: 1.03; }
.hero h1 span, .hero h1 strong { display: block; }
.hero h1 strong { background: linear-gradient(100deg, #173fac, #1c4fd8 50%, #5f8ff5); -webkit-background-clip: text; background-clip: text; color: transparent; }
.hero-desc { max-width: 43rem; margin: 1.6rem auto 0; color: #4b5563; font-size: 1.06rem; }
.hero-actions { display: flex; justify-content: center; gap: .7rem; margin-top: 2rem; }
.fenno-home .btn { display: inline-flex; align-items: center; justify-content: center; gap: .5rem; min-height: 2.8rem; border-radius: 999px; padding: .7rem 1.1rem; font-size: .84rem; font-weight: 650; text-decoration: none; transition: transform .2s ease, box-shadow .2s ease, background .2s ease; }
.fenno-home .btn:hover { transform: translateY(-1px); }
.fenno-home .btn-primary { background: #1c4fd8; box-shadow: 0 10px 24px rgba(28,79,216,.22); color: #fff; }
.fenno-home .btn-primary:hover { background: #173fac; }
.fenno-home .btn-secondary { border: 1px solid #d9dee8; background: #fff; color: #374151; }
.fenno-home .btn-secondary:hover { border-color: #b8d0ff; color: #1c4fd8; }
.hero-badges { display: flex; flex-wrap: wrap; justify-content: center; gap: 1rem 1.4rem; margin-top: 1.5rem; }
.hero-badges span { color: #6b7280; font-size: .72rem; font-weight: 500; letter-spacing: 0; text-transform: none; }
.hero-badges svg { color: #1c4fd8; }
.model-marquee { overflow: hidden; border-top: 1px solid #e5e7eb; border-bottom: 1px solid #e5e7eb; background: #fff; padding: 1rem 0; }
.marquee-track { display: flex; width: max-content; align-items: center; gap: .75rem; animation: marquee 28s linear infinite; }
.model-pill { display: inline-flex; min-width: 9rem; justify-content: center; border: 1px solid #e5e7eb; border-radius: 999px; background: #f9fafb; padding: .55rem .95rem; color: #374151; font-family: ui-monospace, SFMono-Regular, Menlo, monospace; font-size: .76rem; }
@keyframes marquee { from { transform: translateX(0); } to { transform: translateX(-50%); } }
.tools-wall, .feature-strip { border-bottom: 1px solid #e5e7eb; background: #fff; padding: 5rem 0; }
.section-heading { max-width: 43rem; margin: 0 auto 2.4rem; text-align: center; }
.section-heading h2, .config-copy h2, .cta-card h2 { margin: .7rem 0 .65rem; color: #111827; font-size: clamp(1.7rem, 4vw, 2.45rem); font-weight: 750; letter-spacing: -.03em; line-height: 1.1; }
.section-heading p, .config-copy > p, .cta-card p { margin: 0; color: #6b7280; font-size: .96rem; }
.tools-grid { display: grid; grid-template-columns: repeat(2, minmax(0, 1fr)); gap: .8rem; }
.tool-card { display: flex; min-height: 5.5rem; align-items: center; justify-content: center; gap: .65rem; border: 1px solid #edf0f4; border-radius: .9rem; background: #f9fafb; color: #374151; font-size: .86rem; font-weight: 650; transition: border-color .2s, box-shadow .2s, transform .2s; }
.tool-card:hover { border-color: #b8d0ff; box-shadow: 0 10px 28px rgba(28,79,216,.08); transform: translateY(-2px); }
.tool-mark { display: grid; width: 2rem; height: 2rem; place-items: center; border-radius: .55rem; color: #fff; font-size: .68rem; font-weight: 800; }
.tool-mark.blue, .feature-icon.blue { background: #1c4fd8; }.tool-mark.orange { background: #d97745; }.tool-mark.violet, .feature-icon.violet { background: #7856c7; }.tool-mark.rose { background: #d24d74; }.tool-mark.slate { background: #475569; }.tool-mark.cyan { background: #0891b2; }.tool-mark.indigo { background: #4f46a5; }.tool-mark.teal { background: #0f766e; }.tool-mark.green, .feature-icon.green { background: #198754; }
.setup-section { background: #f9fafb; padding: 5.5rem 0; }
.setup-grid, .feature-grid { display: grid; grid-template-columns: 1fr; gap: 1rem; }
.setup-card { position: relative; overflow: hidden; border: 1px solid #e5e7eb; border-radius: 1rem; background: #fff; padding: 1.5rem; }
.step-number { position: absolute; top: 1.1rem; right: 1.3rem; color: #dce8ff; font-size: 2rem; font-weight: 800; }
.step-icon { display: grid; width: 2.7rem; height: 2.7rem; place-items: center; margin-bottom: 1.2rem; border-radius: .7rem; background: #eef4ff; color: #1c4fd8; }
.setup-card h3, .feature-card h3 { margin: 0 0 .45rem; color: #1f2937; font-size: 1rem; font-weight: 700; }
.setup-card p, .feature-card p { margin: 0; color: #6b7280; font-size: .85rem; line-height: 1.65; }
.config-section { background: #111827; padding: 5.5rem 0; }
.config-layout { display: grid; grid-template-columns: 1fr; gap: 2rem; align-items: center; }
.config-copy .eyebrow { color: #8fb0fa; }.config-copy h2 { color: #fff; }.config-copy > p { color: #aab4c2; }
.config-copy ul { display: grid; gap: .65rem; margin: 1.5rem 0 0; padding: 0; list-style: none; color: #dbe4f0; font-size: .86rem; }.config-copy li { display: flex; align-items: center; gap: .55rem; }.config-copy li svg { color: #8fb0fa; }
.config-demo { overflow: hidden; border: 1px solid #334155; border-radius: .8rem; background: #0b1220; box-shadow: 0 20px 50px rgba(0,0,0,.28); }
.config-tabs { display: flex; overflow-x: auto; border-bottom: 1px solid #263447; }.config-tabs button { border: 0; border-bottom: 2px solid transparent; background: transparent; padding: .8rem .95rem; color: #8997aa; font-size: .75rem; cursor: pointer; white-space: nowrap; }.config-tabs button.active { border-bottom-color: #5f8ff5; color: #fff; }
.code-window-bar { display: flex; align-items: center; gap: .35rem; border-bottom: 1px solid #1e293b; padding: .7rem .9rem; color: #7d8a9e; font-size: .7rem; }.code-window-bar span { width: .45rem; height: .45rem; border-radius: 50%; background: #475569; }.code-window-bar span:first-child { background: #f87171; }.code-window-bar span:nth-child(2) { background: #fbbf24; }.code-window-bar span:nth-child(3) { background: #4ade80; }.code-window-bar b { margin-left: .5rem; font-weight: 500; }
.code-window pre { min-height: 14rem; margin: 0; overflow: auto; padding: 1.2rem; color: #cbd5e1; font-family: ui-monospace, SFMono-Regular, Menlo, monospace; font-size: .72rem; line-height: 1.8; white-space: pre-wrap; }
.feature-strip { padding: 5.5rem 0; }.feature-grid { grid-template-columns: 1fr; }.feature-card { border: 1px solid #e5e7eb; border-radius: .9rem; background: #fff; padding: 1.35rem; }.feature-icon { display: grid; width: 2.6rem; height: 2.6rem; place-items: center; margin-bottom: 1rem; border-radius: .65rem; color: #fff; }.feature-icon.violet { background: #7856c7; }
.cta-section { background: #f9fafb; padding: 5.5rem 0; }.cta-card { overflow: hidden; position: relative; border-radius: 1.5rem; background: linear-gradient(120deg, #173fac, #1c4fd8 55%, #5f8ff5); padding: 3.5rem 1.5rem; text-align: center; box-shadow: 0 24px 60px rgba(28,79,216,.22); }.cta-card::after { content: ''; position: absolute; inset: auto -10% -65%; height: 15rem; border-radius: 50%; background: rgba(255,255,255,.12); filter: blur(18px); }.cta-card > * { position: relative; z-index: 1; }.cta-card .eyebrow { color: #dce8ff; }.cta-card h2 { color: #fff; }.cta-card p { max-width: 34rem; margin: 0 auto; color: #dce8ff; }.cta-button { margin-top: 1.5rem; background: #fff; color: #173fac; }.cta-button:hover { background: #eef4ff; }
.public-footer { border-top: 1px solid #e5e7eb; background: #fff; padding: 1.5rem 0; }.public-footer-inner { justify-content: space-between; gap: 1rem; color: #8993a3; font-size: .76rem; }.public-footer-inner p { margin: 0; }.public-footer-links { gap: 1rem; }.public-footer a { color: inherit; text-decoration: none; }.public-footer a:hover { color: #1c4fd8; }
.dark .fenno-home { background: #0f172a; color: #e5e7eb; }.dark .public-header { border-color: #1e293b; background: rgba(15,23,42,.82); }.dark .public-brand, .dark .hero h1, .dark .section-heading h2 { color: #f8fafc; }.dark .public-nav-link, .dark .public-login-link { color: #cbd5e1; }.dark .public-icon-button { color: #94a3b8; }.dark .public-account-link { border-color: #334155; background: #1e293b; color: #cbd5e1; }.dark .public-login-link { background: #f8fafc; color: #0f172a; }.dark .hero-desc, .dark .section-heading p { color: #aab4c2; }.dark .model-marquee, .dark .tools-wall, .dark .feature-strip, .dark .public-footer { border-color: #1e293b; background: #111827; }.dark .model-pill, .dark .tool-card, .dark .feature-card, .dark .setup-card { border-color: #263447; background: #172033; color: #e5e7eb; }.dark .model-pill, .dark .tool-card span:not(.tool-mark), .dark .setup-card h3, .dark .feature-card h3 { color: #e5e7eb; }.dark .setup-section, .dark .cta-section { background: #0f172a; }.dark .setup-card p, .dark .feature-card p { color: #aab4c2; }.dark .public-footer { color: #94a3b8; }
@media (min-width: 640px) { .tools-grid { grid-template-columns: repeat(4, minmax(0, 1fr)); }.setup-grid { grid-template-columns: repeat(3, minmax(0, 1fr)); }.feature-grid { grid-template-columns: repeat(3, minmax(0, 1fr)); }.cta-card { padding: 4.5rem 2rem; } }
@media (min-width: 1024px) { .config-layout { grid-template-columns: .8fr 1.2fr; gap: 4rem; }.tools-grid { grid-template-columns: repeat(5, minmax(0, 1fr)); } }
@media (max-width: 639px) { .container { padding: 0 1rem; }.public-nav { min-height: 3.8rem; }.public-nav-link span, .public-account-link > span:not(.public-avatar), .public-account-link svg { display: none; }.public-nav-actions { gap: .15rem; }.hero { min-height: 40rem; padding-top: 7rem; }.hero-actions { flex-direction: column; align-items: stretch; max-width: 18rem; margin-right: auto; margin-left: auto; }.hero-badges { gap: .7rem; }.hero-badges span { font-size: .67rem; }.tools-wall, .setup-section, .config-section, .feature-strip, .cta-section { padding: 4rem 0; }.public-footer-inner { align-items: flex-start; flex-direction: column; }.cta-card { border-radius: 1rem; } }
</style>
