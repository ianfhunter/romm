<script setup lang="ts">
import InterfaceOption from "@/components/Settings/UserInterface/InterfaceOption.vue";
import RSection from "@/components/common/RSection.vue";
import collectionApi from "@/services/api/collection";
import storeCollections from "@/stores/collections";
import { computed, ref } from "vue";
import { useDisplay } from "vuetify";
import { isNull } from "lodash";
import { useI18n } from "vue-i18n";

// Props
const { t } = useI18n();
const { smAndDown } = useDisplay();
const collectionsStore = storeCollections();

// Initializing refs from localStorage
const storedShowRecentRoms = localStorage.getItem("settings.showRecentRoms");
const showRecentRomsRef = ref(
  isNull(storedShowRecentRoms) ? true : storedShowRecentRoms === "true",
);
const storedShowContinuePlaying = localStorage.getItem(
  "settings.showContinuePlaying",
);
const showContinuePlayingRef = ref(
  isNull(storedShowContinuePlaying)
    ? true
    : storedShowContinuePlaying === "true",
);
const storedShowPlatforms = localStorage.getItem("settings.showPlatforms");
const showPlatformsRef = ref(
  isNull(storedShowPlatforms) ? true : storedShowPlatforms === "true",
);
const storedShowCollections = localStorage.getItem("settings.showCollections");
const showCollectionsRef = ref(
  isNull(storedShowCollections) ? true : storedShowCollections === "true",
);
const storedShowVirtualCollections = localStorage.getItem(
  "settings.showVirtualCollections",
);
const showVirtualCollectionsRef = ref(
  isNull(storedShowVirtualCollections)
    ? true
    : storedShowVirtualCollections === "true",
);
const storedVirtualCollectionType = localStorage.getItem(
  "settings.virtualCollectionType",
);
const virtualCollectionTypeRef = ref(
  isNull(storedVirtualCollectionType)
    ? "collection"
    : storedVirtualCollectionType,
);

const storedGroupRoms = localStorage.getItem("settings.groupRoms");
const groupRomsRef = ref(
  isNull(storedGroupRoms) ? true : storedGroupRoms === "true",
);
const storedSiblings = localStorage.getItem("settings.showSiblings");
const siblingsRef = ref(
  isNull(storedSiblings) ? true : storedSiblings === "true",
);
const storedRegions = localStorage.getItem("settings.showRegions");
const regionsRef = ref(isNull(storedRegions) ? true : storedRegions === "true");
const storedLanguages = localStorage.getItem("settings.showLanguages");
const languagesRef = ref(
  isNull(storedLanguages) ? true : storedLanguages === "true",
);
const storedStatus = localStorage.getItem("settings.showStatus");
const statusRef = ref(isNull(storedStatus) ? true : storedStatus === "true");

const homeOptions = computed(() => [
  {
    title: t("settings.show-recently-added"),
    description: t("settings.show-recently-added-desc"),
    iconEnabled: "mdi-shimmer",
    iconDisabled: "mdi-shimmer",
    model: showRecentRomsRef,
    modelTrigger: toggleShowRecentRoms,
  },
  {
    title: t("settings.show-continue-playing"),
    description: t("settings.show-continue-playing-desc"),
    iconEnabled: "mdi-play",
    iconDisabled: "mdi-play",
    model: showContinuePlayingRef,
    modelTrigger: toggleShowContinuePlaying,
  },
  {
    title: t("settings.show-platforms"),
    description: t("settings.show-platforms-desc"),
    iconEnabled: "mdi-controller",
    iconDisabled: "mdi-controller",
    model: showPlatformsRef,
    modelTrigger: toggleShowPlatforms,
  },
  {
    title: t("settings.show-collections"),
    description: t("settings.show-collections-desc"),
    iconEnabled: "mdi-bookmark-box-multiple",
    iconDisabled: "mdi-bookmark-box-multiple",
    model: showCollectionsRef,
    modelTrigger: toggleShowCollections,
  },
]);

const galleryOptions = computed(() => [
  {
    title: t("settings.group-roms"),
    description: t("settings.group-roms-desc"),
    iconEnabled: "mdi-group",
    iconDisabled: "mdi-ungroup",
    model: groupRomsRef,
    modelTrigger: toggleGroupRoms,
  },
  {
    title: t("settings.show-siblings"),
    description: t("settings.show-siblings-desc"),
    iconEnabled: "mdi-account-group-outline",
    iconDisabled: "mdi-account-outline",
    model: siblingsRef,
    disabled: !groupRomsRef.value,
    modelTrigger: toggleSiblings,
  },
  {
    title: t("settings.show-regions"),
    description: t("settings.show-regions-desc"),
    iconEnabled: "mdi-flag-outline",
    iconDisabled: "mdi-flag-off-outline",
    model: regionsRef,
    modelTrigger: toggleRegions,
  },
  {
    title: t("settings.show-languages"),
    description: t("settings.show-languages-desc"),
    iconEnabled: "mdi-flag-outline",
    iconDisabled: "mdi-flag-off-outline",
    model: languagesRef,
    modelTrigger: toggleLanguages,
  },
  {
    title: t("settings.show-status"),
    description: t("settings.show-status-desc"),
    iconEnabled: "mdi-check-circle-outline",
    iconDisabled: "mdi-close-circle-outline",
    model: statusRef,
    modelTrigger: toggleStatus,
  },
]);

// Functions to update localStorage
const toggleShowRecentRoms = (value: boolean) => {
  showRecentRomsRef.value = value;
  localStorage.setItem("settings.showRecentRoms", value.toString());
};
const toggleShowContinuePlaying = (value: boolean) => {
  showContinuePlayingRef.value = value;
  localStorage.setItem("settings.showContinuePlaying", value.toString());
};
const toggleShowPlatforms = (value: boolean) => {
  showPlatformsRef.value = value;
  localStorage.setItem("settings.showPlatforms", value.toString());
};
const toggleShowCollections = (value: boolean) => {
  showCollectionsRef.value = value;
  localStorage.setItem("settings.showCollections", value.toString());
};
const toggleShowVirtualCollections = (value: boolean) => {
  showVirtualCollectionsRef.value = value;
  localStorage.setItem("settings.showVirtualCollections", value.toString());
};
const setVirtualCollectionType = async (value: string) => {
  virtualCollectionTypeRef.value = value;
  localStorage.setItem("settings.virtualCollectionType", value);

  await collectionApi
    .getVirtualCollections({ type: value })
    .then(({ data: virtualCollections }) => {
      collectionsStore.setVirtual(virtualCollections);
    });
};

const toggleGroupRoms = (value: boolean) => {
  groupRomsRef.value = value;
  localStorage.setItem("settings.groupRoms", value.toString());
};

const toggleSiblings = (value: boolean) => {
  siblingsRef.value = value;
  localStorage.setItem("settings.showSiblings", value.toString());
};

const toggleRegions = (value: boolean) => {
  regionsRef.value = value;
  localStorage.setItem("settings.showRegions", value.toString());
};

const toggleLanguages = (value: boolean) => {
  languagesRef.value = value;
  localStorage.setItem("settings.showLanguages", value.toString());
};

const toggleStatus = (value: boolean) => {
  statusRef.value = value;
  localStorage.setItem("settings.showStatus", value.toString());
};
</script>
<template>
  <r-section
    icon="mdi-palette-swatch-outline"
    :title="t('settings.interface')"
    class="ma-2"
  >
    <template #content>
      <v-chip label variant="text" prepend-icon="mdi-home" class="ml-2 mt-1">{{
        t("settings.home")
      }}</v-chip>
      <v-divider class="border-opacity-25 ma-1" />
      <v-row class="py-1" no-gutters>
        <v-col
          cols="12"
          md="6"
          v-for="option in homeOptions"
          :key="option.title"
        >
          <interface-option
            class="ma-1"
            :title="option.title"
            :description="option.description"
            :icon="
              option.model.value ? option.iconEnabled : option.iconDisabled
            "
            v-model="option.model.value"
            @update:model-value="option.modelTrigger"
          />
        </v-col>
      </v-row>
      <v-chip
        label
        variant="text"
        prepend-icon="mdi-view-grid"
        class="ml-2 mt-1"
        >{{ t("settings.gallery") }}</v-chip
      >
      <v-divider class="border-opacity-25 ma-1" />
      <v-row class="py-1" no-gutters>
        <v-col
          cols="12"
          md="6"
          v-for="option in galleryOptions"
          :key="option.title"
        >
          <interface-option
            class="ma-1"
            :disabled="option.disabled"
            :title="option.title"
            :description="option.description"
            :icon="
              option.model.value ? option.iconEnabled : option.iconDisabled
            "
            v-model="option.model.value"
            @update:model-value="option.modelTrigger"
          />
        </v-col>
      </v-row>
      <v-chip
        label
        variant="text"
        prepend-icon="mdi-view-grid"
        class="ml-2 mt-4"
        >{{ t("common.virtual-collections") }}</v-chip
      >
      <v-divider class="border-opacity-25 mx-2 mb-2" />
      <v-row class="py-1 align-center" no-gutters>
        <v-col cols="12" md="6">
          <interface-option
            class="mx-2"
            :title="t('settings.show-virtual-collections')"
            :description="t('settings.show-virtual-collections-desc')"
            :icon="
              showVirtualCollectionsRef
                ? 'mdi-bookmark-box-multiple'
                : 'mdi-bookmark-box-multiple'
            "
            v-model="showVirtualCollectionsRef"
            @update:model-value="toggleShowVirtualCollections"
          />
        </v-col>
        <v-col cols="12" md="6">
          <v-select
            v-model="virtualCollectionTypeRef"
            :items="[
              { title: 'IGDB Collection', value: 'collection' },
              { title: 'Franchise', value: 'franchise' },
              { title: 'Genre', value: 'genre' },
              { title: 'Play Mode', value: 'mode' },
              { title: 'Developer', value: 'company' },
              { title: 'All (slow)', value: 'all' },
            ]"
            :label="t('settings.virtual-collection-type')"
            class="mx-2"
            :class="{ 'mt-4': smAndDown }"
            variant="outlined"
            hide-details
            :disabled="!showVirtualCollectionsRef"
            @update:model-value="setVirtualCollectionType"
          />
        </v-col>
      </v-row>
    </template>
  </r-section>
</template>
