<template>
  <Nav :class="{ navOpen }" />
  <router-view class="entrypoint" :class="{ hidden: navOpen }" />
  <div v-if="showWAYTOODANK" class="waytoodank">
    <img src="@/assets/waytoodank.webp" />
  </div>
</template>

<script lang="ts">
import { computed, defineComponent, reactive, watch } from "vue";
import Nav from "@/components/Nav.vue";
import { useStore } from "@/store";
import { useHead } from "@vueuse/head";

export default defineComponent({
  components: { Nav },
  setup() {
    const store = useStore();
    const theme = computed(() => {
      switch (store.getters.notFoundMode) {
        case "troll-despair":
          return "troll-despair";
        default:
          return store.getters.theme as "light" | "dark";
      }
    });
    store.commit("SET_THEME", localStorage.getItem("7tv-theme") || "dark");
    const changeCount = computed(() => store.getters.changeCount as number);
    const navOpen = computed(() => store.getters.navOpen as boolean);
    const data = reactive({
      theme,
      changeCount,
      navOpen,
      showWAYTOODANK: false,
    });
    let i: NodeJS.Timeout; // eslint-disable-line

    watch(changeCount, () => {
      if (changeCount.value > 8) {
        data.showWAYTOODANK = true;
        if (i) clearTimeout(i);
        i = setTimeout(() => {
          data.showWAYTOODANK = false;
        }, 4000);
      }
    });
    useHead({
      bodyAttrs: {
        class: computed(() => `theme-${theme.value}`),
      },
    });
    return data;
  },
});
</script>

<style lang="scss">
@import "@/assets/scss/default.scss";
</style>
