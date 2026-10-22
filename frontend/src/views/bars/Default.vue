<template>
  <header v-if="!isOnlyOffice" :class="['flexbar']" :inert="overlayShown">
    <action
      v-if="!disableNavButtons && (isMobile || !isListingView || !isStickySidebar)"
      class="nav-toggle-button"
      icon="close_back"
      :label="isListingView ? 'Open navigation' : $t('general.close')"
      :disabled="isDisabledMultiAction"
      @action="multiAction"
    />
    <a class="drive-brand" href="/" aria-label="Drive home" @click.prevent="goHome">
      <img class="drive-brand__logo" :src="driveLogo" alt="" width="36" height="36" />
      <span>Drive</span>
    </a>
    <div class="search-bar-container"
         v-if="showSearch && !isSearchActive" @click="openSearch" >
      <i class="material-symbols">search</i>
      <input
        type="text"
        id="search-bar-input"
        :placeholder="$t('general.search', { suffix: '...' })"
        readonly
      />
    </div>
    <title v-if="!showSearch" class="topTitle">{{ getTopTitle }}</title>
    <template v-if="!showHeaderSwitchView">
      <action
        class="overflow-menu-button"
        :icon="iconName"
        :disabled="noItems || isSidebarFloating"
        @click="toggleOverflow"
      />
    </template>
    <div class="drive-top-actions" ref="topActions" v-if="!isShare">
      <button type="button" class="drive-icon-button drive-help-button" :aria-label="$t('general.help')" :title="$t('general.help')" @click="showHelp"><i class="material-symbols-outlined" aria-hidden="true">help</i></button>
      <button v-if="settingsAllowed" type="button" class="drive-icon-button drive-settings-button" :aria-label="$t('general.settings')" :title="$t('general.settings')" @click="openSettings"><i class="material-symbols-outlined" aria-hidden="true">settings</i></button>
      <a v-if="workspaceConnected" href="/" class="drive-icon-button drive-workspace-button" data-workspace-apps-trigger aria-label="Open Workspace apps" title="Workspace apps"><svg viewBox="0 0 24 24" width="24" height="24" fill="currentColor" aria-hidden="true"><circle v-for="point in appGridPoints" :key="point.join('-')" :cx="point[0]" :cy="point[1]" r="1.7"/></svg></a>
      <button id="drive-account-button" type="button" class="drive-account-button" :title="displayName" :aria-label="displayName" aria-haspopup="menu" :aria-expanded="accountOpen" aria-controls="drive-account-menu" @click="toggleAccount">{{initials}}</button>
      <div v-if="accountOpen" id="drive-account-menu" class="drive-account-menu" role="menu" :aria-label="$t('general.settings')">
        <div class="drive-account-summary"><strong>{{displayName}}</strong><span v-if="user.username!==displayName">{{user.username}}</span></div>
        <button v-if="settingsAllowed" type="button" role="menuitem" @click="openSettings"><i class="material-symbols-outlined" aria-hidden="true">person</i>{{ $t('general.settings') }}</button>
        <button v-if="showInstall" type="button" role="menuitem" @click="installPwa"><i class="material-symbols-outlined" aria-hidden="true">download</i>{{ $t('pwa.install') }}</button>
        <button v-if="darkModeTogglePossible" type="button" role="menuitemcheckbox" :aria-checked="!!user.darkMode" @click="toggleTheme"><i class="material-symbols-outlined" aria-hidden="true">dark_mode</i>{{ $t('index.toggleDark') }}</button>
        <button v-if="!user.disableQuickToggles" type="button" role="menuitemcheckbox" :aria-checked="!!user.singleClick" @click="toggleClick"><i class="material-symbols-outlined" aria-hidden="true">ads_click</i>{{ $t('index.toggleClick') }}</button>
        <button v-if="settingsAllowed" type="button" role="menuitem" @click="customizeNavigation"><i class="material-symbols-outlined" aria-hidden="true">tune</i>{{ $t('sidebar.customizeLinks') }}</button>
        <button type="button" role="menuitem" @click="showHelp"><i class="material-symbols-outlined" aria-hidden="true">help</i>{{ $t('general.help') }}</button>
        <button v-if="canLogout" type="button" role="menuitem" @click="logout"><i class="material-symbols-outlined" aria-hidden="true">logout</i>{{ $t('general.logout') }}</button>
      </div>
    </div>
  </header>
</template>

<script>
import driveLogo from "@/assets/branding/my_drive.svg";
import { router } from "@/router";
import { getters, state, mutations } from "@/store";
import Action from "@/components/Action.vue";
import { globalVars } from "@/utils/constants";
import { url } from "@/utils";
import {logout as signOut} from '@/utils/auth';
import {installAvailable,promptInstall} from '@/utils/pwaInstall';

export default {
  name: "UnifiedHeader",
  components: {
    Action,
  },
  data() {
    return {
      viewModes: ["list", "normal", "icons"],
      accountOpen: false,
      workspaceConnected: document.documentElement.dataset.workspaceNavigation === 'ready',
      appGridPoints: [[5,5],[12,5],[19,5],[5,12],[12,12],[19,12],[5,19],[12,19],[19,19]],
    };
  },
  computed: {
    user: () => state.user || {},
    showInstall: () => installAvailable.value&&!globalVars.disablePWAInstall,
    isMobile: () => getters.isMobile(),
    isStickySidebar: () => getters.isStickySidebar(),
    settingsAllowed: () => !state.user?.disableSettings,
    canLogout: () => !globalVars.noAuth && state.user?.username && state.user.username!=='anonymous',
    displayName() {return this.user.displayName || this.user.username || 'Account';},
    initials() {return this.displayName.trim().split(/\s+/).slice(0,2).map(part=>part.slice(0,1)).join('').toUpperCase();},
    darkModeTogglePossible: () => !['dark','light'].includes(state.shareInfo?.enforceDarkLightMode),
    driveLogo: () => driveLogo,
    getTopTitle() {
      if (state.route?.name === 'Photos') return 'Photos';
      if (state.route?.name === 'Studio') return 'Studio';
      if (getters.isSettings()) {
        return this.$t("general.settings");
      }
      if (getters.isShare()) {
        if (state.req?.type === "directory" || state.shareInfo?.shareType === "upload") {
          return state.shareInfo?.title;
        }
        return state.req.name;
      }
      const currentTool = getters.currentTool();
      if (currentTool) {
        return currentTool.name;
      }
      return state.req.name;
    },
    disableNavButtons() {
      const isShare = getters.isShare();
      const regularDisabled = globalVars.disableNavButtons && this.isListingView;
      const shareDisabled = isShare && state.shareInfo?.hideNavButtons && getters.currentView() === "listingView";
      const uploadShare = isShare && state.shareInfo?.shareType === "upload"
      return regularDisabled || shareDisabled || uploadShare;
    },
    isOnlyOffice() {
      return getters.currentView() === "onlyOfficeEditor";
    },
    isListingView() {
      return getters.currentView() === "listingView";
    },
    isAdvancedSearchRoute() {
      return (state.route?.path || "") === "/tools/advancedSearch";
    },
    showHeaderSwitchView() {
      return this.isListingView || this.isAdvancedSearchRoute;
    },
    iconName() {
      return getters.currentPromptName() === "OverflowMenu"
        ? "keyboard_arrow_up"
        : "more_vert";
    },
    viewIcon() {
      const icons = {
        list: "view_list",
        compact: "view_list",
        normal: "view_module",
        gallery: "grid_view",
      };
      return icons[getters.viewMode()] || "grid_view";
    },
    isShare() {
      return getters.isShare();
    },
    isSidebarFloating() {
      return getters.isSidebarVisible() && !getters.isStickySidebar();
    },
    noItems() {
      return !state.contextMenuHasItems && !getters.isPreviewView();
    },
    showSearch() {
      return getters.isLoggedIn() && getters.currentView() === "listingView" && !getters.isShare();
    },
    isSearchActive() {
      return state.isSearchActive;
    },
    overlayShown() {
      const prompt=getters.currentPrompt();
      const anchoredMenu=prompt?.name==='ContextMenu'&&prompt.props?.anchorId;
      return state.isSearchActive || (!!getters.currentPromptName() && !anchoredMenu);
    },
    viewModeChangeLocked() {
      return getters.viewModeChangeLocked();
    },
    viewModeActionLabel() {
      if (this.viewModeChangeLocked) {
        return this.$t("profileSettings.enforcedByAdmin");
      }
      return this.$t("buttons.switchView");
    },
    isDisabledMultiAction() {
      const regularDisabled = getters.isStickySidebar() && getters.multibuttonState() === "menu";
      const shareDisabled = state.shareInfo?.disableSidebar && getters.multibuttonState() === "menu";
      return regularDisabled || shareDisabled;
    },
    showSwitchView() {
      return this.showHeaderSwitchView;
    },
    showSidebarToggle() {
      return getters.currentView() === "listingView";
    },
    req() {
      return state.req;
    },
    isSettings() {
      return getters.isSettings();
    },
  },
  mounted() {
    this.workspaceObserver=new MutationObserver(()=>{this.workspaceConnected=document.documentElement.dataset.workspaceNavigation==='ready';});
    this.workspaceObserver.observe(document.documentElement,{attributes:true,attributeFilter:['data-workspace-navigation']});
    document.addEventListener('pointerdown',this.closeAccountOutside,true);
    document.addEventListener('keydown',this.accountKeydown);
  },
  beforeUnmount() {
    this.workspaceObserver?.disconnect();
    document.removeEventListener('pointerdown',this.closeAccountOutside,true);
    document.removeEventListener('keydown',this.accountKeydown);
  },
  watch: {$route(){this.accountOpen=false;}},
  methods: {
    toggleAccount(){this.accountOpen=!this.accountOpen;if(this.accountOpen)this.$nextTick(()=>document.querySelector('#drive-account-menu button')?.focus());},
    closeAccountOutside(event){if(!this.$refs.topActions?.contains(event.target))this.accountOpen=false;},
    accountKeydown(event){if(event.key==='Escape'&&this.accountOpen){event.preventDefault();this.accountOpen=false;document.getElementById('drive-account-button')?.focus();}},
    showHelp(){this.accountOpen=false;mutations.closeHovers();mutations.showPrompt('help');},
    openSettings(){this.accountOpen=false;mutations.closeHovers();mutations.setPreviousHistoryItem({name:state.req.name,source:state.req.source,path:state.req.path,isShare:getters.isShare()});void router.push('/settings');},
    toggleTheme(){mutations.toggleDarkMode();},
    async installPwa(){await promptInstall();this.accountOpen=false;},
    toggleClick(){void mutations.updateCurrentUser({singleClick:!state.user.singleClick});},
    customizeNavigation(){this.accountOpen=false;mutations.closeHovers();mutations.showPrompt('SidebarLinks');},
    logout(){this.accountOpen=false;void signOut();},
    goHome() {
      void router.push("/");
    },
    openSearch() {
      if (!state.isSearchActive) {
        mutations.closeHovers();
        mutations.closeSidebar();
        mutations.resetSelected();
        mutations.setSearch(true);
        // this is hear to allow for animation
        setTimeout(() => {
          const resultList = document.getElementById("result-list");
          resultList.classList.add("active");
          document.getElementById("search-input").focus();
        }, 100);
      }
    },
    toggleOverflow() {
      if (getters.currentPromptName() === "OverflowMenu") {
        mutations.closeHovers();
      } else {
        mutations.showPrompt({ name: "OverflowMenu" });
      }
    },
    /** Match StatusBar.adjustViewMode: list vs compact, icons vs gallery from gallery size. */
    resolveViewModeForFamily(baseMode) {
      const size = state.user?.gallerySize ?? 5;
      if (baseMode === "list") {
        return size <= 3 ? "compact" : "list";
      }
      if (baseMode === "icons") {
        return size <= 4 ? "icons" : "gallery";
      }
      return baseMode;
    },
    /** Map concrete viewMode to one of the three switch-view families (list|compact → list, etc.). */
    viewModeCycleIndex(mode) {
      if (mode === "list" || mode === "compact") {
        return 0;
      }
      if (mode === "normal") {
        return 1;
      }
      if (mode === "gallery" || mode === "icons") {
        return 2;
      }
      return 1;
    },
    switchView() {
      mutations.closeHovers();
      const current = getters.viewMode();
      const cycleIndex = this.viewModeCycleIndex(current);
      const nextIndex = (cycleIndex + 1) % this.viewModes.length;
      const baseMode = this.viewModes.at(nextIndex);
      const newViewMode = this.resolveViewModeForFamily(baseMode);
      mutations.updateDisplayPreferences({ viewMode: newViewMode });
      void mutations.updateCurrentUser({ viewMode: newViewMode });
    },
    multiAction() {
      const cv = getters.currentView();

      // Check for unsaved editor changes before navigation
      if (cv === "editor" && state.editor.dirty) {
        this.showSaveBeforeExitPrompt(() => this.performNavigation(cv));
        return;
      }

      this.performNavigation(cv);
    },
    performNavigation(cv) {
      if (cv === "listingView" || ( getters.isShare() && getters.multibuttonState() !== "close") || cv === "tools") {
        mutations.toggleSidebar();
      } else if (cv === "settings" && getters.isMobile()) {
        mutations.toggleSidebar();
      } else {
        mutations.closeHovers();
        if (cv === "settings") {
          if (state.previousHistoryItem?.name) {
            url.goToItem(
              state.previousHistoryItem.source,
              state.previousHistoryItem.path,
              state.previousHistoryItem,
              false,
              state.previousHistoryItem.isShare
            );
            return;
          }
          if (state.shareInfo?.hash && state.req?.source === state.shareInfo.hash) {
            url.goToItem(state.shareInfo.hash, state.req.path, {}, false, true);
            return;
          }
          // otherwise navigate to files
          void router.push({ path: "/files" });
          return;
        }
        if (getters.isPreviewView()) {
          if (state.previousHistoryItem?.name) {
            url.goToItem(state.previousHistoryItem.source, state.previousHistoryItem.path, state.previousHistoryItem, false, state.previousHistoryItem.isShare);
            return;
          } else {
            // navigate to parent directory of current url
            const parentPath = url.removeLastDir(state.route.path);
            void router.push({ path: parentPath });
          }
          return;
        }

        router.go(-1);
      }
    },
    showSaveBeforeExitPrompt(onConfirmAction) {
      mutations.showPrompt({
        name: "SaveBeforeExit",
        pinned: true,
        confirm: async () => {
          // Save and exit - trigger the save action
          // If save fails, this will throw and be caught by SaveBeforeExit component
          if (!state.editor.saveHandler) {
            throw new Error("No editor save handler registered");
          }
          await state.editor.saveHandler();
          mutations.setEditorDirty(false);
          onConfirmAction();
        },
        discard: () => {
          // Discard changes and exit
          mutations.setEditorDirty(false);
          onConfirmAction();
        },
        cancel: () => {
          // Keep editing - do nothing
        },
      });
    },
  },
};
</script>

<style scoped>
header button:hover {
  box-shadow: unset !important;
}

header {
  background-color: var(--panel-bg);
  backdrop-filter: var(--panel-blur);
  color: var(--textPrimary);
}

:deep(.action.nav-toggle-button .line),
:deep(.action.menu-button),
:deep(.action.overflow-menu-button) {
  stroke: #546e7a;
  color: #546e7a;
}


.search-bar-container {
  display: flex;
  align-items: center;
  background-color: color-mix(in srgb, var(--surfaceSecondary) 75%, transparent);
  border-radius: var(--borderRadius);
  padding: 0.5em 0.75em;
  transition: background-color 0.2s ease;
  gap: 0.5em;
  min-width: 35em;
  max-width: 300px;
  flex: 1;
  height: 3em;
  box-sizing: border-box;
}

.search-bar-container:hover {
  filter:brightness(1.15);
}

#search-bar-input {
  background: transparent;
  border: none;
  outline: none;
  color: rgb(255 255 255 / 90%);
  width: 100%;
  font-size: 0.95em;
  user-select: none;
}

@media (width <= 768px) {
  .search-bar-container {
    min-width: unset;
    max-width: 60%;
  }
}

.search-bar-container .material-symbols {
  font-size: 1.25em;
  user-select: none;
}

#search-bar-input::placeholder {
  color: gray;
}

</style>
