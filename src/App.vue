<template>
  <div class="main">
    <button class="main__button btn btn_outline" @click="togglePopup">
      Налоговый вычет
    </button>
    <tax-popup v-if="showPopup" @close="togglePopup" />
  </div>
</template>

<script>
import TaxPopup from "./components/TaxPopup.vue";

export default {
  name: "App",
  components: { TaxPopup },
  data() {
    return {
      showPopup: false,
    };
  },
  methods: {
    togglePopup() {
      this.showPopup = !this.showPopup;
    },
  },
};
</script>
