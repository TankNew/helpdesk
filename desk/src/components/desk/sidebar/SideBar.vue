<template>
  <div class="z-0 flex select-none flex-col border-r border-gray-200 bg-gray-50 p-2 text-base duration-300 ease-in-out"
    :style="{
      'min-width': width,
      'max-width': width,
    }">
    <UserMenu class="mb-2 ml-0.5" :options="profileSettings" />
    <!-- <SidebarLink
      label="Search"
      class="mb-1"
      :icon="LucideSearch"
      :on-click="() => openCommandPalette()"
      :is-expanded="isExpanded"
    >
      <template #right>
        <span class="flex items-center gap-0.5 font-medium text-gray-600">
          <component :is="device.modifierIcon" class="h-3 w-3" />
          <span>K</span>
        </span>
      </template>
</SidebarLink> -->
    <span class="mb-4">
      <div v-if="notificationStore.unread"
        class="absolute z-20 h-1.5 w-1.5 translate-x-6 translate-y-1 rounded-full bg-gray-800" theme="gray"
        variant="solid" />
      <SidebarLink class="relative" :label="__('Notifications')" :icon="LucideInbox"
        :on-click="() => notificationStore.toggle()" :is-expanded="isExpanded">
        <template #right>
          <Badge v-if="isExpanded && notificationStore.unread" :label="notificationStore.unread" theme="gray"
            variant="subtle" />
        </template>
      </SidebarLink>
    </span>
    <div class="mb-4 flex flex-col gap-1">
      <SidebarLink v-for="option in menuOptions" v-bind="option" :key="option.label" :is-expanded="isExpanded"
        :is-active="option.to?.includes(route.name.toString())" />
    </div>
    <div class="grow" />
    <SidebarLink :icon="isExpanded ? LucideArrowLeftFromLine : LucideArrowRightFromLine" :is-active="false"
      :is-expanded="isExpanded" :label="isExpanded ? 'Collapse' : 'Expand'"
      :on-click="() => (isExpanded = !isExpanded)" />
  </div>
</template>

<script setup lang="ts">
import { computed } from "vue";
import { useRoute, useRouter } from "vue-router";
import { storeToRefs } from "pinia";
import { useAuthStore } from "@/stores/auth";
import { useKeymapStore } from "@/stores/keymap";
import { useNotificationStore } from "@/stores/notification";
import { useSidebarStore } from "@/stores/sidebar";
import {
  AGENT_PORTAL_AGENT_LIST,
  AGENT_PORTAL_CONTACT_LIST,
  AGENT_PORTAL_CUSTOMER_LIST,
  AGENT_PORTAL_TEAM_LIST,
  AGENT_PORTAL_TICKET_LIST,
  CUSTOMER_PORTAL_LANDING,
} from "@/router";
import { useDevice } from "@/composables";
import { SidebarLink } from "@/components";
import UserMenu from "./UserMenu.vue";
import LucideArrowLeftFromLine from "~icons/lucide/arrow-left-from-line";
import LucideArrowRightFromLine from "~icons/lucide/arrow-right-from-line";
import LucideBookOpen from "~icons/lucide/book-open";
import LucideCloudLightning from "~icons/lucide/cloud-lightning";
import LucideContact2 from "~icons/lucide/contact-2";
import LucideInbox from "~icons/lucide/inbox";
import LucideTicket from "~icons/lucide/ticket";
import LucideUser from "~icons/lucide/user";
import LucideUserCircle2 from "~icons/lucide/user-circle-2";
import LucideUsers from "~icons/lucide/users";

const route = useRoute();
const router = useRouter();
const authStore = useAuthStore();
const keymapStore = useKeymapStore();
const notificationStore = useNotificationStore();
const { isExpanded, width } = storeToRefs(useSidebarStore());

const menuOptions = computed(() => [
  {
    label: __("Tickets"),
    icon: LucideTicket,
    to: AGENT_PORTAL_TICKET_LIST,
  },
  {
    label: __("Agents"),
    icon: LucideUser,
    to: AGENT_PORTAL_AGENT_LIST,
  },
  {
    label: __("Knowledge base"),
    icon: LucideBookOpen,
    to: "DeskKBHome",
  },
  {
    label: __("Teams"),
    icon: LucideUsers,
    to: AGENT_PORTAL_TEAM_LIST,
  },
  {
    label: __("Canned responses"),
    icon: LucideCloudLightning,
    to: "CannedResponses",
  },
  {
    label: __("Customers"),
    icon: LucideUserCircle2,
    to: AGENT_PORTAL_CUSTOMER_LIST,
  },
  {
    label: __("Contacts"),
    icon: LucideContact2,
    to: AGENT_PORTAL_CONTACT_LIST,
  },
]);

const profileSettings = [
  {
    label: __('Shortcuts'),
    icon: "command",
    onClick: () => keymapStore.toggleVisibility(true),
  },
  {
    label: __('Customer portal'),
    icon: "users",
    onClick: () => {
      const path = router.resolve({ name: CUSTOMER_PORTAL_LANDING });
      window.open(path.href, "_blank");
    },
  },
  {
    label: __('Switch To Desk'),
    icon: "users",
    onClick: () => {
      window.location.href = '/app'
    },
  },
  {
    label: __('Log out'),
    icon: "log-out",
    onClick: () => authStore.logout(),
  },
];

// function openCommandPalette() {
//   window.dispatchEvent(
//     new KeyboardEvent("keydown", { key: "k", metaKey: true })
//   );
// }
</script>
