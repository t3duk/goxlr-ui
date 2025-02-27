<script>
import Mixer from "@/components/sections/lighting/subTabs/LightingMixer.vue";
import Effects from "@/components/sections/lighting/subTabs/LightingEffects.vue";
import Sampler from "@/components/sections/lighting/subTabs/LightingSampler.vue";
import Cough from "@/components/sections/lighting/subTabs/LightingCough.vue";
import Global from "@/components/sections/lighting/subTabs/LightingGlobal.vue";

import {isDeviceMini} from "@/util/util";
import CenteredContainer from "@/components/containers/CenteredContainer.vue";

export default {
  name: "LightingTab",
  components: {
    CenteredContainer,
    Mixer,
    Effects,
    Sampler,
    Cough,
    Global,
  },

  data() {
    return {
      currentTab: 'Global',
    }
  },

  methods: {
    getTabs() {
      return isDeviceMini() ? ['Global', 'Mixer', 'Cough'] : ['Global', 'Mixer', 'Effects', 'Sampler', 'Cough']
    },
    onTabKeydown(event) {
      const tabs = this.getTabs();
      const activeTab = this.currentTab;
      const activeTabIndex = tabs.indexOf(activeTab);
      let nextTab;

      if (event.key === "ArrowRight" || event.key === "ArrowDown") {
        nextTab = tabs[(activeTabIndex + 1) % tabs.length];
        //explanation: if activeTabIndex is 0, then 0+1 % 3 = 1, so nextTab is tabs[1]
      } else if (event.key === "ArrowLeft" || event.key === "ArrowUp") {
        nextTab =
            tabs[(activeTabIndex - 1 + tabs.length) % tabs.length];
        //explanation: if activeTabIndex is 0, then 0-1+3 % 3 = 2, so nextTab is tabs[2]
      } else if (event.key === "Home") {
        nextTab = tabs[0];
      } else if (event.key === "End") {
        nextTab = tabs[tabs.length - 1];
      }

      if (nextTab) {
        this.currentTab = nextTab;
        //we need a ref on the button element to focus it
        this.$refs[nextTab][0].focus();
      }
    }
  }
}
</script>

<template>
  <CenteredContainer class="sections" role="tablist" aria-label="Lighting Settings">
    <button v-for="tab in getTabs()" :key="tab" :class="['button', { active: currentTab === tab }]"
            @click="currentTab = tab" role="tab" :aria-selected="currentTab === tab" :aria-controls="tab.toLowerCase()"
            :tabindex="currentTab === tab ? 0 : -1" :ref="tab" @keydown="onTabKeydown">
      {{ tab }}
    </button>
  </CenteredContainer>
  <component :is="currentTab" role="tabpanel" :aria-label="currentTab"/>
</template>

<style scoped>

.sections > button:first-child {
  border-radius: 5px 0 0 5px;
}

.sections > button:last-child {
  border-radius: 0 5px 5px 0;
}

.sections {
  gap: 2px;
}

.button {
  min-width: 100px;
  padding: 6px 6px;
  width: fit-content;

  margin-top: 15px;

  color: #ffffff;
  background-color: #353937;
  border: none;
  cursor: pointer;
}

.button:not(.active):hover {
  background-color: #49514e;
}

.button.active {
  color: #2b2f2d;
  background-color: #59b1b6;
}
</style>
