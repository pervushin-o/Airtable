<script setup lang="ts">
import tinycolor from 'tinycolor2'
import {
  TabType,
  computed,
  definePageMeta,
  extractSdkResponseErrorMsg,
  iconMap,
  isDrawerOrModalExist,
  isMac,
  message,
  navigateTo,
  onBeforeMount,
  onBeforeUnmount,
  onKeyStroke,
  onMounted,
  openLink,
  projectThemeColors,
  ref,
  resolveComponent,
  storeToRefs,
  useCopy,
  useDialog,
  useGlobal,
  useI18n,
  useProject,
  useRoute,
  useRouter,
  useSidebar,
  useTabs,
  useTheme,
  useUIPermission,
} from '#imports'

definePageMeta({
  hideHeader: true,
})

const { theme, defaultTheme } = useTheme()

const { t } = useI18n()

const { $e } = useNuxtApp()

const { betaFeatureToggleState } = useBetaFeatureToggle()

const route = useRoute()

const router = useRouter()

const { appInfo, token, signOut, signedIn, user, currentVersion, isMobileMode, setIsMobileMode } = useGlobal()

const projectStore = useProject()

const { loadProjectMetaInfo, saveTheme, loadProject, reset } = projectStore
const { project, isSharedBase, projectMetaInfo } = storeToRefs(projectStore)

const { clearTabs, addTab } = useTabs()

const { isUIAllowed } = useUIPermission()

const { copy } = useCopy(true)

// create a new sidebar state
const { isOpen, toggle, toggleHasSidebar } = useSidebar('nc-left-sidebar', { hasSidebar: false, isOpen: false })

const dialogOpen = ref(false)

const openDialogKey = ref<string>('')

const dataSourcesState = ref<string>('')

const dropdownOpen = ref(false)

/** Sidebar ref */
const sidebar = ref()

const email = computed(() => user.value?.email ?? '---')

const logout = async () => {
  await signOut()
  navigateTo('/signin')
}

function toggleDialog(value?: boolean, key?: string, dsState?: string) {
  dialogOpen.value = value ?? !dialogOpen.value
  openDialogKey.value = key || ''
  dataSourcesState.value = dsState || ''
}

provide(ToggleDialogInj, toggleDialog)

const handleThemeColor = async (mode: 'swatch' | 'primary' | 'accent', color?: string) => {
  switch (mode) {
    case 'swatch': {
      if (color === defaultTheme.primaryColor) {
        return await saveTheme(defaultTheme)
      }

      const tcolor = tinycolor(color)
      if (tcolor.isValid()) {
        const complement = tcolor.complement()

        await saveTheme({
          primaryColor: color,
          accentColor: complement.toHex8String(),
        })
      }
      break
    }
    case 'primary': {
      const tcolor = tinycolor(color)

      if (tcolor.isValid()) {
        await saveTheme({
          primaryColor: color,
        })
      }
      break
    }
    case 'accent': {
      const tcolor = tinycolor(color)

      if (tcolor.isValid()) {
        await saveTheme({
          accentColor: color,
        })
      }
      break
    }
  }
}

const copyProjectInfo = async () => {
  try {
    await loadProjectMetaInfo()

    if (
      await copy(
        Object.entries(projectMetaInfo.value!)
          .map(([k, v]) => `${k}: **${v}**`)
          .join('\n'),
      )
    ) {
      // Copied to clipboard
      message.info(t('msg.info.copiedToClipboard'))
    }
  } catch (e: any) {
    console.error(e)
    message.error(e.message)
  }
}

const copyAuthToken = async () => {
  try {
    if (await copy(token.value!)) {
      // Copied to clipboard
      message.info(t('msg.info.copiedToClipboard'))
    }
  } catch (e: any) {
    console.error(e)
    message.error(e.message)
  }
}

onKeyStroke(
  'Escape',
  () => {
    dropdownOpen.value = false
  },
  { eventName: 'keydown' },
)

onBeforeMount(async () => {
  try {
    await loadProject()
  } catch (e: any) {
    if (e.response?.status === 403) {
      // Project is not accessible
      message.error(t('msg.error.projectNotAccessible'))
      router.replace('/')
      return
    }
    message.error(await extractSdkResponseErrorMsg(e))
  }

  if (route.name === 'projectType-projectId-index-index' && isUIAllowed('teamAndAuth')) {
    addTab({ id: TabType.AUTH, type: TabType.AUTH, title: t('title.teamAndAuth') })
  }

  /** If v1 url found navigate to corresponding new url */
  const { type, name, view } = route.query
  if (type && name) {
    await router.replace(`/nc/${route.params.projectId}/${type}/${name}${view ? `/${view}` : ''}`)
  }
})

onMounted(() => {
  toggle(true)
  toggleHasSidebar(true)
})

onBeforeUnmount(() => {
  clearTabs()
  reset()
})

function openKeyboardShortcutDialog() {
  $e('a:actions:keyboard-shortcut')

  const isOpen = ref(true)

  const { close } = useDialog(resolveComponent('DlgKeyboardShortcuts'), {
    'modelValue': isOpen,
    'onUpdate:modelValue': closeDialog,
  })

  function closeDialog() {
    isOpen.value = false
    close(1000)
  }
}

useEventListener(document, 'keydown', async (e: KeyboardEvent) => {
  const cmdOrCtrl = isMac() ? e.metaKey : e.ctrlKey
  if (e.altKey && !e.shiftKey && !cmdOrCtrl) {
    switch (e.keyCode) {
      case 188: {
        // ALT + ,
        if (isUIAllowed('settings') && !isDrawerOrModalExist()) {
          e.preventDefault()
          $e('c:shortcut', { key: 'ALT + ,' })
          toggleDialog(true, 'teamAndAuth')
        }
        break
      }
    }
  }
  if (cmdOrCtrl) {
    switch (e.key) {
      case '/':
        if (!isDrawerOrModalExist()) {
          $e('c:shortcut', { key: 'CTRL + /' })
          openKeyboardShortcutDialog()
        }
        break
    }
  }
})
</script>

<template>
  <NuxtLayout>
    <template #sidebar>
      <a-layout-sider
        ref="sidebar"
        :collapsed="!isOpen"
        width="250"
        collapsed-width="50"
        class="relative shadow-md h-full z-1 nc-left-sidebar"
        :trigger="null"
        collapsible
        theme="light"
      >
        <div
          style="height: var(--header-height); border-bottom-width: 1px"
          :class="isOpen ? 'pl-4' : ''"
          class="flex items-center text-primary px-1 gap-1 nc-sidebar-header"
        >
          <div
            v-if="isOpen && !isSharedBase"
            v-e="['c:navbar:home']"
            data-testid="nc-noco-brand-icon"
            class="w-[29px] min-w-[29px] transition-all duration-200 py-1 pl-1 cursor-pointer transform hover:scale-105 nc-noco-brand-icon"
            @click="navigateTo('/')"
          >
            <a-tooltip placement="bottom">
              <template #title>
                {{ currentVersion }}
              </template>
              <img width="25" class="-mr-1" alt="NocoDB" src="~/assets/img/icons/512x512.png" />
            </a-tooltip>
          </div>

          <a
            v-if="isOpen && isSharedBase"
            class="w-[40px] min-w-[40px] transition-all duration-200 p-1 cursor-pointer transform hover:scale-105"
            href="https://github.com/nocodb/nocodb"
            target="_blank"
          >
            <a-tooltip placement="bottom">
              <template #title>
                {{ currentVersion }}
              </template>
              <img width="25" alt="NocoDB" src="~/assets/img/icons/512x512-trans.png" />
            </a-tooltip>
          </a>

          <a-dropdown
            class="h-full min-w-0 flex-1"
            :trigger="['click']"
            placement="bottom"
            overlay-class-name="nc-dropdown-project-menu"
          >
            <div
              :style="{ width: isOpen ? 'calc(100% - 40px) pr-2' : '100%' }"
              :class="[isOpen ? '' : 'justify-center']"
              data-testid="nc-project-menu"
              class="group cursor-pointer flex gap-1 items-center nc-project-menu overflow-hidden"
            >
              <template v-if="isOpen">
                <a-tooltip v-if="project.title?.length > 12" placement="bottom">
                  <div class="text-md font-semibold truncate">{{ project.title }}</div>
                  <template #title>
                    <div class="text-sm">{{ project.title }}</div>
                  </template>
                </a-tooltip>
                <div v-else class="text-md font-semibold truncate capitalize">{{ project.title }}</div>
                <component :is="iconMap.arrowDown" class="min-w-[17px] group-hover:text-accent text-md" />
              </template>

              <template v-else>
                <component :is="iconMap.folder" class="text-primary cursor-pointer transform hover:scale-105 text-2xl" />
              </template>
            </div>

            <template #overlay>
              <a-menu class="!ml-1 !w-[300px] !text-sm">
                <a-menu-item-group>
                  <template #title>
                    <div class="group select-none flex items-center gap-4 py-1">
                      <component :is="iconMap.folder" class="group-hover:text-accent text-xl" />

                      <div class="flex flex-col">
                        <div class="text-lg group-hover:(!text-primary) font-semibold capitalize">
                          <GeneralTruncateText>{{ project.title }}</GeneralTruncateText>
                        </div>

                        <div v-if="!isSharedBase" class="flex items-center gap-1">
                          <div class="group-hover:(!text-primary)">ID:</div>

                          <div class="text-xs group-hover:text-accent truncate font-italic">{{ project.id }}</div>
                        </div>
                      </div>
                    </div>
                  </template>
                  <template v-if="!isSharedBase">
                    <!-- Copy Project Info -->
                    <a-menu-item key="copy">
                      <div
                        v-e="['c:navbar:user:copy-proj-info']"
                        class="nc-project-menu-item group"
                        @click.stop="copyProjectInfo"
                      >
                        <component :is="iconMap.copy" class="group-hover:text-accent" />
                        {{ $t('activity.account.projInfo') }}
                      </div>
                    </a-menu-item>

                    <a-menu-divider />

                    <!-- Swagger: Rest APIs -->
                    <a-menu-item key="api">
                      <div
                        v-if="isUIAllowed('apiDocs') && !isMobileMode"
                        v-e="['e:api-docs']"
                        class="nc-project-menu-item group"
                        @click.stop="openLink(`/api/v1/db/meta/projects/${route.params.projectId}/swagger`, appInfo.ncSiteUrl)"
                      >
                        <component :is="iconMap.json" class="group-hover:text-accent" />
                        {{ $t('activity.account.swagger') }}
                      </div>
                    </a-menu-item>

                    <!-- Copy Auth Token -->
                    <a-menu-item key="copy">
                      <div
                        v-if="!isMobileMode"
                        v-e="['a:navbar:user:copy-auth-token']"
                        class="nc-project-menu-item group"
                        @click.stop="copyAuthToken"
                      >
                        <component :is="iconMap.copy" class="group-hover:text-accent" />
                        {{ $t('activity.account.authToken') }}
                      </div>
                    </a-menu-item>

                    <a-menu-divider />

                    <!-- Team & Settings -->
                    <a-menu-item key="teamAndSettings">
                      <div
                        v-if="isUIAllowed('settings') && !isMobileMode"
                        v-e="['c:navdraw:project-settings']"
                        class="nc-project-menu-item group"
                        @click="toggleDialog(true, 'teamAndAuth')"
                      >
                        <component :is="iconMap.settings" class="group-hover:text-accent" />
                        {{ $t('title.teamAndSettings') }}
                      </div>
                    </a-menu-item>

                    <!-- Mobile Mode -->
                    <a-menu-item v-if="betaFeatureToggleState.show || isMobileMode" key="mobile-mode">
                      <div
                        v-e="['e:set-mobile-mode']"
                        class="nc-project-menu-item group"
                        @click.stop="setIsMobileMode(!isMobileMode)"
                      >
                        <MaterialSymbolsMobileFriendly class="group-hover:text-accent" />
                        {{ $t('activity.toggleMobileMode') }}
                      </div>
                    </a-menu-item>

                    <!-- Theme -->
                    <template v-if="isUIAllowed('projectTheme')">
                      <a-sub-menu key="theme">
                        <template #title>
                          <div class="nc-project-menu-item group">
                            <component :is="iconMap.image" class="group-hover:text-accent" />
                            {{ $t('activity.account.themes') }}

                            <div class="flex-1" />

                            <MaterialSymbolsChevronRightRounded
                              class="transform group-hover:(scale-115 text-accent) text-xl text-gray-400"
                            />
                          </div>
                        </template>

                        <template #expandIcon></template>

                        <LazyGeneralColorPicker
                          :model-value="theme.primaryColor"
                          :colors="projectThemeColors"
                          :row-size="9"
                          :advanced="false"
                          class="rounded-t"
                          @input="handleThemeColor('swatch', $event)"
                        />

                        <!-- Custom Theme -->
                        <a-sub-menu key="theme-2">
                          <template #title>
                            <div class="nc-project-menu-item group">
                              {{ $t('labels.customTheme') }}

                              <div class="flex-1" />

                              <MaterialSymbolsChevronRightRounded
                                class="transform group-hover:(scale-115 text-accent) text-xl text-gray-400"
                              />
                            </div>
                          </template>

                          <!-- Primary Color -->
                          <template #expandIcon></template>

                          <a-sub-menu key="pick-primary">
                            <template #title>
                              <div class="nc-project-menu-item group">
                                <ClarityColorPickerSolid class="group-hover:text-accent" />
                                {{ $t('labels.primaryColor') }}
                              </div>
                            </template>

                            <template #expandIcon></template>

                            <LazyGeneralChromeWrapper @input="handleThemeColor('primary', $event)" />
                          </a-sub-menu>

                          <!-- Accent Color -->
                          <a-sub-menu key="pick-accent">
                            <template #title>
                              <div class="nc-project-menu-item group">
                                <ClarityColorPickerSolid class="group-hover:text-accent" />
                                {{ $t('labels.accentColor') }}
                              </div>
                            </template>

                            <template #expandIcon></template>

                            <LazyGeneralChromeWrapper @input="handleThemeColor('accent', $event)" />
                          </a-sub-menu>
                        </a-sub-menu>
                      </a-sub-menu>
                    </template>

                    <a-menu-divider />

                    <!-- Preview As -->
                    <a-sub-menu v-if="isUIAllowed('previewAs') && !isMobileMode" key="preview-as">
                      <template #title>
                        <div v-e="['c:navdraw:preview-as']" class="nc-project-menu-item group">
                          <component :is="iconMap.preview" class="group-hover:text-accent" />
                          {{ $t('activity.previewAs') }}

                          <div class="flex-1" />

                          <MaterialSymbolsChevronRightRounded
                            class="transform group-hover:(scale-115 text-accent) text-xl text-gray-400"
                          />
                        </div>
                      </template>

                      <template #expandIcon></template>

                      <LazyGeneralPreviewAs />
                    </a-sub-menu>
                  </template>
                  <!-- Language -->
                  <a-sub-menu
                    key="language"
                    class="lang-menu !py-0"
                    popup-class-name="scrollbar-thin-dull min-w-50 max-h-90vh !overflow-auto"
                  >
                    <template #title>
                      <div class="nc-project-menu-item group">
                        <component :is="iconMap.translate" class="group-hover:text-accent nc-language" />
                        {{ $t('labels.language') }}
                        <div class="flex items-center text-gray-400 text-xs">(Community Translated)</div>
                        <div class="flex-1" />

                        <MaterialSymbolsChevronRightRounded
                          class="transform group-hover:(scale-115 text-accent) text-xl text-gray-400"
                        />
                      </div>
                    </template>

                    <template #expandIcon></template>

                    <LazyGeneralLanguageMenu />
                  </a-sub-menu>

                  <!-- Account -->
                  <template v-if="signedIn && !isSharedBase">
                    <a-sub-menu key="account">
                      <template #title>
                        <div class="nc-project-menu-item group">
                          <component :is="iconMap.account" class="group-hover:text-accent" />
                          {{ $t('labels.account') }}
                          <div class="flex-1" />

                          <MaterialSymbolsChevronRightRounded
                            class="transform group-hover:(scale-115 text-accent) text-xl text-gray-400"
                          />
                        </div>
                      </template>

                      <template #expandIcon></template>

                      <a-menu-item key="0" class="!rounded-t">
                        <nuxt-link
                          v-e="['c:navbar:user:email']"
                          class="nc-project-menu-item group !no-underline"
                          to="/account/users"
                        >
                          <component :is="iconMap.at" class="mt-1 group-hover:text-accent" />&nbsp;
                          <div class="prose-sm group-hover:text-primary">
                            <div>Account</div>
                            <div class="text-xs text-gray-500">{{ email }}</div>
                          </div>
                        </nuxt-link>
                      </a-menu-item>

                      <a-menu-item key="1" class="!rounded-b">
                        <div v-e="['a:navbar:user:sign-out']" class="nc-project-menu-item group" @click="logout">
                          <component :is="iconMap.signout" class="group-hover:(!text-accent)" />&nbsp;

                          <span class="prose-sm nc-user-menu-signout">
                            {{ $t('general.signOut') }}
                          </span>
                        </div>
                      </a-menu-item>
                    </a-sub-menu>
                  </template>
                </a-menu-item-group>
              </a-menu>
            </template>
          </a-dropdown>

          <div
            class="nc-sidebar-left-toggle-icon hover:after:(bg-primary bg-opacity-75) group nc-sidebar-add-row flex items-center px-2"
          >
            <component
              :is="iconMap.sidebarMinimise"
              v-e="['c:grid:toggle-navdraw']"
              class="cursor-pointer transform transition-transform duration-500"
              :class="{ 'rotate-180': !isOpen }"
              @click="toggle(!isOpen)"
            />
          </div>
        </div>

        <LazyDashboardTreeView @create-base-dlg="toggleDialog(true, 'dataSources')" />
      </a-layout-sider>
    </template>

    <div>
      <LazyDashboardSettingsModal
        v-model:model-value="dialogOpen"
        v-model:open-key="openDialogKey"
        v-model:data-sources-state="dataSourcesState"
      />
      <NuxtPage :page-key="$route.params.projectId" />
      <LazyGeneralPreviewAs float />
    </div>
  </NuxtLayout>
</template>

<style lang="scss" scoped>
:global(#nc-sidebar-left .ant-layout-sider-collapsed) {
  @apply !w-0 !max-w-0 !min-w-0 overflow-x-hidden;
}

.nc-left-sidebar {
  .nc-sidebar-left-toggle-icon {
    @apply opacity-0 transition-opactity duration-200 transition-color text-gray-500/80 hover:text-gray-500/100;

    .nc-left-sidebar {
      @apply !border-r-0;
    }
  }

  &:hover .nc-sidebar-left-toggle-icon {
    @apply opacity-100;
  }
}

:deep(.ant-dropdown-menu-submenu-title) {
  @apply py-0;
}

.nc-sidebar-header {
  @apply border-[var(--navbar-border)] !bg-[var(--navbar-bg)];
}
</style>
