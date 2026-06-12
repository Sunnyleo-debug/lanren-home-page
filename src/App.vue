<template>
  <v-app class="vapp-fullscreen-background" style="overflow: hidden;"
  :style="{height: '100%',width: '100%',top: '0',left:'0'}">
    <transition name="fade">
      <div class="loading" v-show="isloading">
        <loader></loader>
      </div>
    </transition>

    <video autoplay loop muted class="video-bg" id="bg-video" ref="VdPlayer"
    :style="{height: '100%',width: '100%',top: '0',left:'0'}">
        <source :src="videosrc" :type="videoType">
    </video>

    <div class="floating-switch-container">
      <v-switch
        v-model="isClearScreen"
        inset
        :style="xs?{'transform':'scale(0.6) translateX(15%)'}:{}"
        class="floating-switch"
        @mouseover="expandSwitch"
        @mouseleave="collapseSwitch"
      ></v-switch>
    </div>

    <div v-show="!isloading && !isClearScreen" class="floating-music">
      <transition name="slide-y-transition">
        <v-card v-show="isFloatingMusicOpen" class="floating-music__panel" variant="tonal">
          <div class="floating-music__meta">
            <v-avatar :image="currentSong?.pic" size="42"></v-avatar>
            <div class="floating-music__text">
              <div class="floating-music__title">{{ currentSong?.title || 'Music' }}</div>
              <div class="floating-music__author">{{ currentSong?.author || '网易云歌单 / NetEase' }}</div>
            </div>
          </div>
          <div class="floating-music__controls">
            <v-btn icon="mdi-skip-previous" size="small" variant="tonal" @click="previousTrack"></v-btn>
            <v-btn :icon="isPlaying ? 'mdi-pause' : 'mdi-play'" size="small" variant="tonal" @click="togglePlay"></v-btn>
            <v-btn icon="mdi-skip-next" size="small" variant="tonal" @click="nextTrack"></v-btn>
          </div>
        </v-card>
      </transition>
      <v-btn
        class="floating-music__button"
        :icon="isFloatingMusicOpen ? 'mdi-chevron-down' : 'mdi-music-note'"
        color="var(--leleo-vcard-color)"
        variant="tonal"
        @click="toggleFloatingMusic"
      ></v-btn>
    </div>

    <audio v-show="false" ref="audioPlayer" preload="none"
      @waiting="onWaiting"
      @canplay="onCanPlay">
    </audio>
    
    <div v-show="!isloading && !isClearScreen" class="site-shell">
      <homeright :configdata=configdata :formattedTime=formattedTime 
      :formattedDate=formattedDate :projectcards=projectcards></homeright>
    </div>

    <div v-show="!isloading && !isClearScreen" class="floating-settings" :class="{ 'is-open': settingsPanelOpen }">
      <transition name="slide-y-transition">
        <div v-show="settingsPanelOpen" class="floating-settings__panel">
          <button class="floating-settings__option" type="button" @click="openSettingsDialog">
            <v-icon icon="mdi-tune-variant" size="20"></v-icon>
            <span>
              <strong>设置 / Settings</strong>
              <small>样式、壁纸、音乐</small>
            </span>
          </button>
          <button class="floating-settings__option" type="button" @click="openAboutDialog">
            <v-icon icon="mdi-information-outline" size="20"></v-icon>
            <span>
              <strong>关于 / About</strong>
              <small>技术栈和说明</small>
            </span>
          </button>
        </div>
      </transition>
      <v-btn
        class="floating-settings__button"
        :icon="settingsPanelOpen ? 'mdi-close' : 'mdi-cog'"
        color="var(--leleo-vcard-color)"
        variant="tonal"
        :aria-expanded="settingsPanelOpen"
        aria-label="Open settings"
        @click.stop="toggleSettingsPanel"
      ></v-btn>
    </div>

    <v-dialog
        v-model="dialog1"
        :width="xs || sm ? 'calc(100vw - 24px)' : 1000"
        max-width="1000"
        class="settings-dialog"
      >
      <v-card class="settings-dialog__card" elevation="3">
        <v-tabs
          v-model="tab"
          :items="tabs"
          align-tabs="center"
          height="60"
          slider-color=var(--leleo-vcard-color)
        >
          <template v-slot:tab="{ item }">
            <v-tab
              :prepend-icon="item.icon"
              :text="item.text"
              :value="item.value"
              class="text-none"
            ></v-tab>
          </template>
          
          <template v-slot:item="{ item }">
            <v-tabs-window-item :value="item.value" class="pa-4">
              <div v-if="item.value=='tab-3' && musicinfoLoading" class="loading-spinner" align="center">
                  <v-progress-circular indeterminate></v-progress-circular>
              </div>
              <!-- 通过组件绑定不同tab项的组件 -->
              <component v-if="item.value!='tab-3' || (item.value=='tab-3' && !musicinfoLoading)" :is=item.component @cancel="handleCancel" 
              :musicinfo="item.value=='tab-3'?musicinfo:[]"
              :currentIndex="item.value=='tab-3'?playlistIndex:null"
              :isPlaying="item.value=='tab-3'?isPlaying:null"
              :audioPlayer="item.value=='tab-3'?audioPlayer:null"
              :fromLyrics="item.value=='tab-3'?lyrics:null"
              :audioLoading="item.value=='tab-3'?audioLoading:null"
              @update:current-index="updateCurrentIndex"
              @update:is-playing="updateIsPlaying"
              @update:current-lyrics="updateLyrics"
              ></component>
            </v-tabs-window-item>
          </template>
        </v-tabs>
      </v-card>
      </v-dialog>

      <v-dialog
        v-model="dialog2"
        :width="xs || sm ? 'calc(100vw - 24px)' : 700"
        max-width="700"
      >
      <v-card class="ma-3 pa-2" hover
          variant="tonal"
          rounded="lg"
          style="text-align: center;backdrop-filter: blur(10px);"
        >
          <template v-slot:title >
            <span class="leleo-card-title">关于 / About</span>
          </template>
          <div style="display: flex;flex-direction: column;align-items: center;">
            <v-card class="ma-3 pa-2" hover
              variant="tonal"
              max-width="400"
              rounded="lg"
              style="text-align: center;"
              >
              <template v-slot:subtitle>
                <span class="leleo-card-subtitle">本页基于以下技术及服务搭建 / Built with these technologies</span>
              </template>
              <div>
                <v-tooltip  v-for="item in stackicons" v-model="item.model" location="top">
                  <template v-slot:activator="{ props }">
                    <v-btn icon v-bind="props" :color=item.color rounded="lg" class="ma-1 stack-btn" size="35">
                      <v-icon size="25" color="white">{{item.icon}}</v-icon>
                    </v-btn>
                  </template>
                  <span>{{item.tip}}</span>
                </v-tooltip>
                <!-- 自定义 -->
                <v-tooltip location="top">
                  <template v-slot:activator="{ props }">
                    <v-btn v-bind="props" rounded="lg" class="ma-1 stack-btn" size="35">
                      <v-avatar image="/img/stackicon/vite.svg" rounded="0" size="23"></v-avatar>
                    </v-btn>
                  </template>
                  <span>vite</span>
                </v-tooltip>
                <v-tooltip location="top">
                  <template v-slot:activator="{ props }">
                    <v-btn v-bind="props" rounded="lg" class="ma-1 stack-btn" size="35" color="#254B7C">
                      <span style="font-size: 8px;font-weight: bolder;">{less}</span>
                    </v-btn>
                  </template>
                  <span>less</span>
                </v-tooltip>
                <v-tooltip location="top">
                  <template v-slot:activator="{ props }">
                    <v-btn v-bind="props" rounded="lg" class="ma-1 stack-btn" size="35">
                      <v-avatar image="/img/stackicon/mdi.svg" rounded="0" size="35"></v-avatar>
                    </v-btn>
                  </template>
                  <span>mdi</span>
                </v-tooltip>
                <v-tooltip location="top">
                  <template v-slot:activator="{ props }">
                    <v-btn v-bind="props" rounded="lg" class="ma-1 stack-btn" size="35">
                      <v-avatar image="/img/stackicon/chartjs.png" rounded="0" size="23"></v-avatar>
                    </v-btn>
                  </template>
                  <span>chartjs</span>
                </v-tooltip>
                <v-tooltip location="top">
                  <template v-slot:activator="{ props }">
                    <v-btn v-bind="props" rounded="lg" class="ma-1 stack-btn" size="35" color="#0F1225">
                      <v-avatar image="/img/stackicon/meting.png" rounded="0" size="23"></v-avatar>
                    </v-btn>
                  </template>
                  <span>meting</span>
                </v-tooltip>
                <v-tooltip location="top">
                  <template v-slot:activator="{ props }">
                    <v-btn v-bind="props" rounded="lg" class="ma-1 stack-btn" size="35" color="#070707">
                      <v-avatar image="/img/stackicon/uiverse.png" rounded="0" size="23"></v-avatar>
                    </v-btn>
                  </template>
                  <span>uiverse</span>
                </v-tooltip>
              </div>
            </v-card>

            <p class="ma-6">
                <span v-for="item in configdata.statement">
                  {{ item }}<br>
                </span>
            </p>
          </div>
        </v-card>
    </v-dialog>
  </v-app>
</template>

<script src="./app.js"></script>
<style scoped>
  @import url(/css/app.less);
</style>
