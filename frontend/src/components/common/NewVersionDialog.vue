<script setup lang="ts">
import storeHeartbeat from "@/stores/heartbeat";
import semver from "semver";
import { onMounted, ref } from "vue";
import { useDisplay } from "vuetify";

// Props
const heartbeat = storeHeartbeat();
const { VERSION } = heartbeat.value.SYSTEM;
const GITHUB_VERSION = ref(VERSION);
const latestVersionDismissed = ref(VERSION === "development");

function dismissVersionBanner() {
  localStorage.setItem("dismissedVersion", GITHUB_VERSION.value);
  latestVersionDismissed.value = true;
}
onMounted(async () => {
  try {
    const response = await fetch(
      "https://api.github.com/repos/rommapp/romm/releases/latest",
    );
    const json = await response.json();
    GITHUB_VERSION.value = json.tag_name;
    const publishedAt = new Date(json.published_at);
    latestVersionDismissed.value =
      // Hide if the version is not valid
      !semver.valid(VERSION) ||
      // Hide if the version is the same as the dismissed version
      json.tag_name === localStorage.getItem("dismissedVersion") ||
      // Hide if the version is less than 2 hours old
      publishedAt.getTime() + 2 * 60 * 60 * 1000 > Date.now();
  } catch (error) {
    console.error("Failed to fetch latest version from Github", error);
  }
});
</script>

<template>
  <div v-if="semver.valid(VERSION)" class="pa-3 sticky-bottom">
    <v-slide-y-transition>
      <v-card
        v-if="
          GITHUB_VERSION &&
          semver.gt(GITHUB_VERSION, VERSION) &&
          !latestVersionDismissed
        "
        class="pa-1 border-selected mx-auto"
        max-width="250"
      >
        <v-card-text class="text-center py-2 px-4">
          <span class="text-white text-shadow">New version available</span>
          <span class="text-primary ml-1">v{{ GITHUB_VERSION }}</span>
          <v-row class="mt-1" no-gutters>
            <v-col>
              <span class="pointer text-grey" @click="dismissVersionBanner"
                >Dismiss</span
              ><span class="ml-4"
                ><a
                  target="_blank"
                  :href="`https://github.com/rommapp/romm/releases/tag/${GITHUB_VERSION}`"
                  >See what's new!</a
                ></span
              >
            </v-col>
          </v-row>
        </v-card-text>
      </v-card>
    </v-slide-y-transition>
  </div>
</template>
<style scoped>
.sticky-bottom {
  position: fixed;
  bottom: 0;
  left: 0;
  width: 100%;
  z-index: 9999;
  pointer-events: none;
}
.sticky-bottom * {
  pointer-events: auto; /* Re-enables pointer events for all child elements */
}
</style>
