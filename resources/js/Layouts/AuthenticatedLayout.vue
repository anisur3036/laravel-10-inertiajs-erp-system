<script setup>
import { ref, onMounted } from 'vue'
import {
  BeakerIcon,
  MagnifyingGlassIcon,
  HomeIcon,
  UsersIcon,
  UserIcon,
  CheckIcon,
} from '@heroicons/vue/24/solid'

import ApplicationLogo from '@/Components/ApplicationLogo.vue'
import Dropdown from '@/Components/Dropdown.vue'
import DropdownLink from '@/Components/DropdownLink.vue'
import NavLink from '@/Components/NavLink.vue'
import ResponsiveNavLink from '@/Components/ResponsiveNavLink.vue'
import { Head, Link } from '@inertiajs/vue3'

const showingNavigationDropdown = ref(false)

onMounted(() => {
  const menuBtn = document.querySelector('.menu-btn')
  const sideMenu = document.querySelector('#sidebar')
  const closeBtn = document.querySelector('.close-btn')
  const mainContent = document.querySelector('#main-container')

  menuBtn.addEventListener('click', (e) => {
    sideMenu.classList.toggle('active')
    mainContent.classList.toggle('active')
  })

  closeBtn.addEventListener('click', function () {
    if (
      sideMenu.classList.contains('active') &&
      mainContent.classList.contains('active')
    ) {
      sideMenu.classList.remove('active')
      mainContent.classList.remove('active')
    }
  })
})

const menus = ref([
  {
    title: 'Dashboard',
    open: false,
    subMenu: [{ name: 'Dashboard', url: '/dashboard' }],
  },
  {
    title: 'Internet',
    open: false,
    subMenu: [{ name: 'Internet', url: '/internets' }],
  },
  {
    title: 'Department',
    open: false,
    subMenu: [{ name: 'Department', url: '/departments' }],
  },
  {
    title: 'Designation',
    open: false,
    subMenu: [{ name: 'Designation', url: '/designations' }],
  },
])
const closeAllMenus = (item) => {
  if (!item.open) {
    menus.value.forEach((el) => (el.open = false))
  }
  item.open = !item.open
}

const timer = ref(0)
setInterval(() => timer.value++, 1000)
</script>

<template>
  <div id="sidebar" class="sidebar">
    <div
      id="brand"
      class="fixed sm:left-0 h-14 top-0 w-64 py-4 pr-0 pl-4 text-white shadow"
    >
      <div class="flex items-center justify-between">
        <a href="#" class="flex items-center font-bold"
          ><ApplicationLogo class="block h-9 w-auto" />
          <h2 class="pl-2 text-2xl">SKCL</h2></a
        >
        <button class="close-btn sm:hidden cursor-pointer mr-2">
          <svg
            xmlns="http://www.w3.org/2000/svg"
            fill="none"
            viewBox="0 0 24 24"
            stroke-width="1.5"
            stroke="currentColor"
            class="w-6 h-6"
          >
            <path
              stroke-linecap="round"
              stroke-linejoin="round"
              d="M6 18L18 6M6 6l12 12"
            />
          </svg>
        </button>
      </div>
    </div>
    <nav id="sidebar-menu" class="mt-14 mb-8 overflow-y-scroll h-screen pb-14">
      <section class="px-4">
        <h3 class="py-4 text-sm font-semibold text-gray-500 overflow-hidden">
          Menu
        </h3>
        <div class="-px-2">
          <div class="faq_field" v-for="(menu, i) in menus" :key="i">
            <h3
              @click="closeAllMenus(menu)"
              class="submenu-header my-1 py-2 px-4 rounded  flex items-center justify-between cursor-pointer text-gray-300"
              :class="menu.open ? 'bg-gray-800' : ''"
            >
              <span class="shrink-0 flex-1">{{ menu.title }}</span
              ><span class="" :class="menu.open ? 'rotate' : ''">+</span>
            </h3>
            <div
              class="invisible text-gray-300 rounded bg-gray-900"
              :class="menu.open ? 'visible' : ''"
            >
              <div class="flex flex-col" v-for="menuItem in menu.subMenu">
                <Link
                  :href="menuItem.url"
                  :class="{ 'text-red-600': $page.url === menuItem.url }"
                  class="my-1 cursor-pointer"
                  >{{ menuItem.name }}</Link
                >
              </div>
            </div>
          </div>
        </div>
      </section>
      <section class="px-4">
        <h3 class="py-4 text-sm font-semibold text-gray-500 overflow-hidden">
          Section
        </h3>
        <div class="pl-2 flex flex-col space-y-3 py-2">
          <a
            class="flex items-center text-gray-400 hover:text-gray-300"
            href="#"
            ><span class="flex-none">Menu Items</span></a
          >
        </div>
      </section>
    </nav>
  </div>
  <div id="main-container" class="main-container">
    <header
      class="flex items-center justify-between fixed left-0 h-14 sm:left-64 bg-white w-full sm:w-[calc(100%-256px)] top-0 z-50 p-2 shadow"
    >
      <div class="flex items-center">
        <button class="menu-btn">
          <svg
            xmlns="http://www.w3.org/2000/svg"
            fill="none"
            viewBox="0 0 24 24"
            stroke-width="1.5"
            stroke="currentColor"
            class="w-6 h-6 cursor-pointer"
          >
            <path
              stroke-linecap="round"
              stroke-linejoin="round"
              d="M3.75 6.75h16.5M3.75 12h16.5m-16.5 5.25h16.5"
            />
          </svg>
        </button>
        <span>Welcome {{ timer }} </span>
      </div>
      <div class="relative flex items-center sm:w-[400px]">
        <MagnifyingGlassIcon
          class="w-5 h-5 absolute left-[8px] text-gray-400"
        />
        <input
          class="pl-8 w-full py-1 rounded-full border-gray-300 focus:ring-1 focus:ring-gray-50"
          type="text"
          name="search"
          placeholder="Search..."
        />
      </div>
      <div class="ml-3 relative">
        <Dropdown align="right" width="48">
          <template #trigger>
            <span class="inline-flex rounded-md">
              <button
                type="button"
                class="inline-flex items-center px-3 py-2 border border-transparent text-sm leading-4 font-medium rounded-md text-gray-500 bg-white hover:text-gray-700 focus:outline-none transition ease-in-out duration-150"
              >
                {{ $page.props.auth.user.name }}

                <svg
                  class="ml-2 -mr-0.5 h-4 w-4"
                  xmlns="http://www.w3.org/2000/svg"
                  viewBox="0 0 20 20"
                  fill="currentColor"
                >
                  <path
                    fill-rule="evenodd"
                    d="M5.293 7.293a1 1 0 011.414 0L10 10.586l3.293-3.293a1 1 0 111.414 1.414l-4 4a1 1 0 01-1.414 0l-4-4a1 1 0 010-1.414z"
                    clip-rule="evenodd"
                  />
                </svg>
              </button>
            </span>
          </template>

          <template #content>
            <DropdownLink :href="route('profile.edit')">
              Profile
            </DropdownLink>
            <DropdownLink :href="route('logout')" method="post" as="button">
              Log Out
            </DropdownLink>
          </template>
        </Dropdown>
      </div>
    </header>
    <main class="mt-14 min-h-[calc(100vh)] bg-gray-100">
      <slot />
    </main>
  </div>
</template>

<style scoped>
.invisible {
  visibility: hidden;
  opacity: 0;
  height: 0;
  transition: height 0.3s, visibility 0.3s;
}

.visible {
  visibility: visible;
  min-height: 100px;
  height: auto;
  opacity: 1;
  padding: 10px 20px;
}
.open_faq {
  cursor: pointer;
}

.rotate {
  transition: all 0.3s;
  transform: rotate(45deg);
}
</style>

