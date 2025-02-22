<template>
  <Subscription v-if="toggleSubscription" />
  <div>
    <div id="dropdown" />
    <div class="md:flex md:flex-col">
      <div class="md:flex md:flex-col md:h-screen">
        <div class="md:flex md:shrink-0">
          <div class="flex items-center justify-between px-6 py-4 bg-indigo-900 md:shrink-0 md:justify-center md:w-56">
            <Link class="mt-1" :href="route('admin.dashboard')">
            <logo class="fill-white" width="120" height="28" />
            </Link>
            <dropdown class="md:hidden" placement="bottom-end">
              <template #default>
                <svg class="w-6 h-6 fill-white" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20">
                  <path d="M0 3h20v2H0V3zm0 6h20v2H0V9zm0 6h20v2H0v-2z" />
                </svg>
              </template>
              <template #dropdown>
                <div class="mt-2 px-8 py-4 bg-indigo-800 rounded shadow-lg">
                  <main-menu />
                </div>
              </template>
            </dropdown>
          </div>
          <div
            class="md:text-md flex items-center justify-between p-4 w-full text-sm bg-white border-b md:px-12 md:py-0">
            <div class="mr-4 mt-1"></div>
            <dropdown class="mt-1" placement="bottom-end">
              <template #default>
                <div class="group flex items-center cursor-pointer select-none">
                  <div class="mr-1 text-gray-700 group-hover:text-indigo-600 focus:text-indigo-600 whitespace-nowrap">
                    <span>{{ auth.user.name }}</span>
                  </div>
                  <icon class="w-5 h-5 fill-gray-700 group-hover:fill-indigo-600 focus:fill-indigo-600"
                    name="cheveron-down" />
                </div>
              </template>
              <template #dropdown>
                <div class="mt-2 py-2 text-sm bg-white rounded shadow-xl">
                  <Link class="block px-6 py-2 hover:text-white hover:bg-indigo-500" :href="route('admin.profile.edit')">My Profile
                  </Link>
                  <Link class="block px-6 py-2 w-full text-left hover:text-white hover:bg-indigo-500"
                    :href="route('logout')" method="delete" as="button">Logout</Link>
                </div>
              </template>
            </dropdown>
          </div>
        </div>
        <div class="md:flex md:grow md:overflow-hidden">
          <main-menu class="hidden shrink-0 p-12 w-56 bg-indigo-800 overflow-y-auto md:block" />
          <div class="w-full md:overflow-y-auto">
            <div class="px-4 py-8 md:flex-1 md:p-12" scroll-region>
              <flash-messages />
              <slot />
            </div>
            <Footer :daysLeft="daysLeft"/>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { Link } from '@inertiajs/vue3'
import Icon from '@/Shared/Icon.vue'
import Logo from '@/Shared/Logo.vue'
import Dropdown from '@/Shared/Dropdown.vue'
import MainMenu from '@/Shared/MainMenu.vue'
import FlashMessages from '@/Shared/FlashMessages.vue'
import Subscription from '@/Shared/Subscription.vue'
import axios from "axios";
import Footer from '@/Shared/Footer.vue'

export default {
  components: {
    Dropdown,
    FlashMessages,
    Icon,
    Link,
    Logo,
    MainMenu,
    Subscription,
    Footer,
  },
  props: {
    auth: Object,
  },
  data(){
    return {
      toggleSubscription: false,
      daysLeft : null,
    }
  },
  async mounted() {
    let subscription = null;
    let url = route('admin.subscription')
    const res = await axios.get(url);
    subscription = res.data;
    let today = new Date();
    let subscriptionEndDate = new Date(subscription.subscription_end);
    this.toggleSubscription = (today > subscriptionEndDate ? true : false);
    let timeDifference = subscriptionEndDate - today;
    this.daysLeft = Math.floor(timeDifference / (1000 * 60 * 60 * 24));
  }
}
</script>
