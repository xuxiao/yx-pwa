<template>
    <div id="app">
        <transition
            :name="pageTransitionEffect">
            <keep-alive
                :include="[...keepAlivePages]">
                <router-view
                    :key="routerViewKey"
                    class="app-view"
                    :class="[pageTransitionClass]"
                    ></router-view>
            </keep-alive>
        </transition>
        <update-toast></update-toast>
        <offline-toast></offline-toast>
    </div>
</template>

<script>
import { mapState } from "vuex";
import UpdateToast from "@/components/UpdateToast";
import OfflineToast from "@/components/OfflineToast";
import { keepAlivePages } from "@/.lavas/router";


import(/* webpackChunkName: "fastclick" */ 'fastclick').then(FastClick => FastClick.attach(document.body));

export default {
  name: "app",
  components: {
    UpdateToast,
    OfflineToast
  },
  computed: {
    ...mapState("pageTransition", {
      pageTransitionType: state => state.type,
      pageTransitionEffect: state => state.effect
    }),

    pageTransitionClass() {
      return `transition-${this.pageTransitionType}`;
    },

    // https://github.com/lavas-project/lavas/issues/119
    routerViewKey() {
      let { name, params } = this.$route;
      let paramKeys = Object.keys(params);
      if (paramKeys.length) {
        return name + paramKeys.reduce((prev, cur) => prev + params[cur], "");
      }
      return null;
    }
  },
  data() {
    return {
      // https://github.com/lavas-project/lavas/issues/112
      keepAlivePages
    };
  },
  mounted() {
    this.$store.dispatch("main/getCartCommodity");
  }
};
</script>


<style lang="scss">
@import "~@/assets/styles/global.scss";
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;

  .app-view {
    position: absolute;
    top: 0;
    right: 0;
    bottom: 0;
    left: 0;
    overflow-x: hidden;
    overflow-y: auto;
    background: white;
    &::-webkit-scrollbar {
      width: 0;
      background: transparent;
    }
    &.transition-slide {
      transition: transform 0.4s cubic-bezier(0.55, 0, 0.1, 1);
      &.slide-left-enter {
        transform: translate(100%, 0);
      }
      &.slide-right-enter {
        transform: translate(-100%, 0);
      }
      &.slide-right-leave-active {
        transform: translate(100%, 0);
      }
      &.slide-left-leave-active {
        transform: translate(-100%, 0);
      }
    }
    &.transition-fade {
      opacity: 1;
      transition: opacity 1s ease;

      &.fade-enter {
        opacity: 0;
      }

      &.fade-leave-active {
        opacity: 0;
      }
    }

    // &.transition-slide-fade
    //     &.slide-fade-enter-active
    //         transition: all .3s ease
    //
    //     &.slide-fade-leave-active
    //         transition: all .8s cubic-bezier(1.0, 0.5, 0.8, 1.0)
    //
    //     &.slide-fade-enter,
    //     &.slide-fade-leave-to
    //         transform: translateX(10px)
    //         opacity: 0
  }
}
</style>
