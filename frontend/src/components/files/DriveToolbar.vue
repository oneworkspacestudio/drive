<template>
  <div class="drive-folder-toolbar">
    <h1>{{ title }}</h1>
    <div class="drive-folder-actions">
      <div class="drive-view-switch" role="group" aria-label="File display">
        <button type="button" :disabled="locked" :aria-pressed="listView" aria-label="List view" title="List view" @click="setView('list')"><i class="material-symbols-outlined" aria-hidden="true">view_list</i></button>
        <button type="button" :disabled="locked" :aria-pressed="!listView" aria-label="Grid view" title="Grid view" @click="setView('normal')"><i class="material-symbols-outlined" aria-hidden="true">grid_view</i></button>
      </div>
      <button type="button" class="drive-icon-button" :aria-label="$t('general.info')" :title="$t('general.info')" @click="showInfo"><i class="material-symbols-outlined" aria-hidden="true">info</i></button>
    </div>
  </div>
</template>
<script setup>
import {computed} from 'vue';
import {getters,mutations,state} from '@/store';
const title=computed(()=>state.req.path==='/'||!state.req.path?state.req.source||state.req.name:state.req.name);
const listView=computed(()=>['list','compact'].includes(getters.viewMode()));
const locked=computed(()=>getters.viewModeChangeLocked());
function setView(mode){
  if(locked.value)return;
  const resolved=mode==='list'&&(state.user?.gallerySize??5)<=3?'compact':mode;
  mutations.updateDisplayPreferences({viewMode:resolved});
  void mutations.updateCurrentUser({viewMode:resolved});
}
function showInfo(){mutations.closeHovers();mutations.showPrompt({name:'info',props:{item:state.req}});}
</script>
<style scoped>
.drive-folder-toolbar{display:flex;align-items:center;justify-content:space-between;gap:16px;min-height:64px;padding:8px 16px;background:var(--drive-surface)}
.drive-folder-toolbar h1{min-width:0;margin:0;font-size:26px;font-weight:400;color:var(--textPrimary);overflow:hidden;text-overflow:ellipsis;white-space:nowrap}
.drive-folder-actions{display:flex;align-items:center;gap:12px;flex:none}.drive-view-switch{display:flex;border:1px solid var(--drive-outline);border-radius:var(--drive-radius-full);overflow:hidden}
.drive-view-switch button{display:grid;place-items:center;width:56px;height:36px;padding:0;border:0;background:var(--drive-surface);color:var(--textPrimary);cursor:pointer}.drive-view-switch button+button{border-left:1px solid var(--drive-outline)}
.drive-view-switch button[aria-pressed='true']{background:var(--drive-selected);color:var(--drive-on-selected)}.drive-view-switch button:hover{background:var(--drive-surface-high)}.drive-view-switch button[aria-pressed='true']:hover{background:var(--drive-selected-hover)}
.drive-view-switch button:focus-visible{outline:3px solid var(--drive-primary);outline-offset:-3px}.drive-view-switch button:disabled{cursor:default;opacity:.5}
@media(max-width:600px){.drive-folder-toolbar{padding:8px;gap:8px;min-height:56px}.drive-folder-toolbar h1{font-size:22px}.drive-folder-actions{gap:4px}.drive-view-switch button{width:42px}}
</style>
