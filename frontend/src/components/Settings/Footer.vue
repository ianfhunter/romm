<script setup lang="ts">
import storeHeartbeat from "@/stores/heartbeat";
import storeNavigation from "@/stores/navigation";
import { useDisplay } from "vuetify";
import { storeToRefs } from "pinia";
import { computed } from "vue";

// Props
const { smAndDown } = useDisplay();
const heartbeatStore = storeHeartbeat();
const navigationStore = storeNavigation();
const { mainBarCollapsed } = storeToRefs(navigationStore);
// Computed property for dynamic width
const computedWidth = computed(() => {
  return smAndDown.value
    ? "100% !important"
    : mainBarCollapsed.value
      ? "calc(100% - 60px) !important"
      : "calc(100% - 100px) !important";
});
</script>
<template>
  <div
    class="position-fixed"
    :class="{
      'bottom-0': !smAndDown,
      'bottom-50': smAndDown,
    }"
    :style="{ width: computedWidth }"
  >
    <v-card class="bg-toplayer ma-2 pa-2">
      <v-row class="align-center justify-center" no-gutters>
        <v-hover v-slot="{ isHovering, props }">
          <a
            :href="`https://github.com/rommapp/romm/releases/tag/${heartbeatStore.value.SYSTEM.VERSION}`"
            target="_blank"
            rel="noopener noreferrer"
            class="text-decoration-none text-primary"
            v-bind="props"
            :class="{
              'text-secondary': isHovering,
            }"
          >
            <!-- <code class="px-2 py-1 text-primary"> -->
            {{ heartbeatStore.value.SYSTEM.VERSION }}
            <!-- </code> -->
          </a>
        </v-hover>
        <v-icon>mdi-circle-small</v-icon>
        <v-hover v-slot="{ isHovering, props }">
          <a
            href="https://github.com/rommapp/romm"
            target="_blank"
            rel="noopener noreferrer"
            class="text-decoration-none text-primary"
            v-bind="props"
            :class="{
              'text-secondary': isHovering,
            }"
            >Github</a
          >
        </v-hover>
        <v-icon>mdi-circle-small</v-icon>
        <v-hover v-slot="{ isHovering, props }">
          <a
            href="https://discord.com/invite/P5HtHnhUDH"
            target="_blank"
            rel="noopener noreferrer"
            class="text-decoration-none text-primary"
            v-bind="props"
            :class="{
              'text-secondary': isHovering,
            }"
            >Discord</a
          >
        </v-hover>
      </v-row>
    </v-card>
  </div>
</template>
