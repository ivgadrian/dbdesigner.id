<template>
  <div>
    <information-alert />
    <warning-alert />
    <ribbon-menu />
    <canvas-layout />
    <table-detail />
    <left-panel />
    <export />
    <fork/>
    <loading-global />
  </div>
</template>

<script>
import qs from "querystringify";
import { mapMutations } from "vuex";
import { mapActions } from "vuex";
import LoadingGlobal from "./components/Utill/LoadingGlobal/Layout";
import RibbonMenu from "./components/TopMenu/Layout.vue";
import LeftPanel from "./components/LeftDialog/FileMenu/Layout.vue";
import Export from "./components/RightDialog/Export/Layout";
import Fork from "./components/RightDialog/Fork/Layout";
import TableDetail from "./components/RightDialog/TableDetail/Layout.vue";
import { message } from "ant-design-vue";
import InformationAlert from "./components/TopAlert/Information/Layout";
import WarningAlert from "./components/TopAlert/Warning/Layout";
import bagroundPatternImage from "@/assets/canvas-background.png";
import CanvasLayout from "./components/MainCanvas/Layout";
import Bowser from "bowser";
export default {
  components: {
    CanvasLayout,
    InformationAlert,
    WarningAlert,
    RibbonMenu,
    LeftPanel,
    Export,
    LoadingGlobal,
    TableDetail,
    Fork
  },
  methods: {
    ...mapMutations("LeftDialog/FileMenu/Layout", {
      leftPanelSetVisible: "setVisible",
      leftPanelSetPanelName: "setPanelName"
    }),
    ...mapMutations("RightDialog/Export/Component/Image", {
      SET_IMAGE_BASE_64: "SET_IMAGE_BASE_64"
    }),
    ...mapActions("Data/Project", {
      loadProject: "loadProject",
      setEmptyDiagram: "setEmptyDiagram",
      autoSave: "autoSave"
    }),
    ...mapActions("Data/Account", {
      globalReadAccount: "globalReadAccount"
    }),
    ...mapMutations("TopAlert/Warning/Layout", {
      setWarningBrowserVisible: "setVisible",
      setWarningBrowserMessage: "setMessage"
    })
  },
  created() {
    this.setEmptyDiagram();
  },
  async mounted() {
    const bagroundPattern = new window.Image();
    bagroundPattern.src = bagroundPatternImage;
    bagroundPattern.onload = () => {
      this.bagroundPattern = bagroundPattern;
    };
    if (window.location.toString().indexOf("uuid=") > 1) {
      var valueUUID = window.location.toString().split("uuid=")[1];
      this.globalReadAccount({
        uuid: valueUUID
      });
      this.loadProject({
        uuid: valueUUID
      });
    } else {
      await this.globalReadAccount({
        uuid: null
      });
      var parsed = qs.parse(window.location.toString().split("#")[1]);
      if (parsed.src === "mail_confirmation") {
        if (parsed.action === "re_login") {
          this.leftPanelSetVisible(true);
          this.leftPanelSetPanelName("login");
          message.success(parsed.message, 7);
        } else if (parsed.action === "open_project") {
          this.leftPanelSetVisible(true);
          this.leftPanelSetPanelName("open");
          message.success(parsed.message, 7);
        } else if (parsed.action === "error_email_confirmation_expired") {
          this.leftPanelSetVisible(true);
          this.leftPanelSetPanelName("login");
          message.error(parsed.message, 7);
        } else if (parsed.action === "token_not_valid") {
          this.leftPanelSetVisible(true);
          this.leftPanelSetPanelName("login");
          message.error(parsed.message, 7);
        }
      } else if (parsed.src === "registration") {
        if (parsed.action === "new_project") {
          this.leftPanelSetVisible(true);
          this.leftPanelSetPanelName("new");
          message.success(parsed.message, 7);
        }
      }
    }

    const browser = Bowser.getParser(window.navigator.userAgent);
    if(browser.getBrowserName().toLowerCase().indexOf('chrome')<0){
      this.setWarningBrowserMessage(
        "You detect use "+browser.getBrowserName()+"!!!  "+
        "Use Chrome browser for better experience 😃😃😃"
      );
      this.setWarningBrowserVisible(true);
    }

    this.autoSave();
  },
  computed: {},
  data() {
    return {
      bagroundPattern: null,
      scaleBy: 1.01
    };
  }
};
</script>

<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
