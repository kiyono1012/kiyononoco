<script lang="ts" setup>
import MdiAt from '~icons/mdi/at'
import MdiLogout from '~icons/mdi/logout'
import MdiDotsVertical from '~icons/mdi/dots-vertical'
import MaterialSymbolsMenu from '~icons/material-symbols/menu'
import { navigateTo } from '#app'

const { $state } = useNuxtApp()

const sidebar = ref<HTMLDivElement>()

const email = computed(() => $state.user?.value?.email ?? '---')

const signOut = () => {
  $state.signOut()
  navigateTo('/signin')
}

const toggleSidebar = useToggle($state.sidebarOpen)

const sidebarOpen = computed({
  get: () => !$state.sidebarOpen.value,
  set: (val) => toggleSidebar(val),
})
</script>

<template>
  <a-layout>
    <a-layout-header class="flex !bg-primary items-center text-white px-4 shadow-md">
      <MaterialSymbolsMenu
        v-if="$state.signedIn.value"
        class="text-xl cursor-pointer"
        @click="toggleSidebar(!$state.sidebarOpen.value)"
      />

      <div class="flex-1" />

      <div class="ml-4 flex justify-center flex-1">
        <div class="flex items-center gap-2 cursor-pointer nc-noco-brand-icon" @click="navigateTo('/')">
          <img width="35" src="~/assets/img/icons/512x512-trans.png" />
          <span class="prose-xl">NocoDB</span>
        </div>

        <!-- todo: loading is not yet supported by nuxt 3 - see https://v3.nuxtjs.org/migration/component-options#loading
          <span v-show="$nuxt.$loading.show" class="caption grey--text ml-3">
            {{ $t('general.loading') }} <v-icon small color="grey">mdi-spin mdi-loading</v-icon>
          </span>


          todo: replace shortkey?
          <span v-shortkey="['ctrl', 'shift', 'd']" @shortkey="openDiscord" />
           -->
      </div>

      <div class="flex-1" />

      <div class="flex justify-end gap-4">
        <general-language class="mr-3" />

        <template v-if="$state.signedIn.value">
          <a-dropdown :trigger="['click']">
            <MdiDotsVertical class="md:text-xl cursor-pointer nc-user-menu" @click.prevent />

            <template #overlay>
              <a-menu class="!py-0 nc-user-menu min-w-32 dark:(!bg-gray-800) leading-8 !rounded">
                <a-menu-item key="0" class="!rounded-t">
                  <nuxt-link v-t="['c:navbar:user:email']" class="group flex items-center no-underline py-2" to="/user">
                    <MdiAt class="mt-1 group-hover:text-success" />&nbsp;
                    <span class="prose group-hover:text-black nc-user-menu-email">{{ email }}</span>
                  </nuxt-link>
                </a-menu-item>

                <a-menu-divider class="!m-0" />

                <a-menu-item key="1" class="!rounded-b">
                  <div v-t="['a:navbar:user:sign-out']" class="group flex items-center py-2" @click="signOut">
                    <MdiLogout class="dark:text-white group-hover:(!text-red-500)" />&nbsp;
                    <span class="prose font-semibold text-gray-500 group-hover:text-black nc-user-menu-signout">{{
                      $t('general.signOut')
                    }}</span>
                  </div>
                </a-menu-item>
              </a-menu>
            </template>
          </a-dropdown>
        </template>
      </div>
    </a-layout-header>

    <a-layout>
      <a-layout-sider
        v-model:collapsed="sidebarOpen"
        width="300"
        collapsed-width="0"
        class="bg-white dark:!bg-gray-800 border-r-1 border-gray-200 dark:!border-gray-600 h-full"
        :trigger="null"
        collapsible
      >
        <div id="sidebar" ref="sidebar" class="w-full h-full" />
      </a-layout-sider>

      <NuxtPage />
    </a-layout>
  </a-layout>
</template>
