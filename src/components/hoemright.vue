<template>
      <main class="portfolio-page">
          <section id="home" class="hero-stage">
            <div class="hero-stage__topline">
              <div class="hero-stage__brand">
                <span>烂人</span>
                <small>LANREN</small>
              </div>
              <form class="hero-search hero-search--top" @submit.prevent="performSearch">
                <button class="hero-search__submit" type="submit" aria-label="Search or open URL">
                  <v-icon :icon="isUrl ? 'mdi-earth' : 'mdi-magnify'" size="18"></v-icon>
                </button>
                <input
                  v-model="searchQuery"
                  type="text"
                  placeholder="Search / 输入关键词或网址"
                  aria-label="Search keywords or open URL"
                  @focus="searchEngineMenuOpen = false"
                  @keydown.esc="searchEngineMenuOpen = false"
                >
                <div class="hero-search__engine-wrap">
                  <button
                    class="hero-search__engine"
                    type="button"
                    :aria-expanded="searchEngineMenuOpen"
                    @click.stop="searchEngineMenuOpen = !searchEngineMenuOpen"
                  >
                    <span>{{ selectedEngine.title }}</span>
                    <v-icon icon="mdi-chevron-down" size="16"></v-icon>
                  </button>
                  <div v-if="searchEngineMenuOpen" class="hero-search__menu" @click.stop>
                    <button
                      v-for="engine in searchEngines"
                      :key="engine.value"
                      type="button"
                      :class="{ 'is-active': selectedEngine.value === engine.value }"
                      @click="selectSearchEngine(engine)"
                    >
                      {{ engine.title }}
                    </button>
                  </div>
                </div>
              </form>
              <div class="hero-stage__nav">
                <div class="hero-stage__links">
                  <button type="button" @click="scrollToSection('profile')">Profile</button>
                  <button type="button" @click="scrollToSection('projects')">Projects</button>
                  <button type="button" @click="scrollToSection('advantages')">Advantages</button>
                  <button type="button" @click="copyText(configdata.contact.wechat)">Contact</button>
                </div>
                <button
                  class="hero-menu-toggle"
                  type="button"
                  :aria-expanded="mobileNavOpen"
                  aria-label="Open navigation"
                  @click="toggleMobileNav"
                >
                  <v-icon :icon="mobileNavOpen ? 'mdi-close' : 'mdi-menu'" size="22"></v-icon>
                </button>
                <transition name="fade">
                  <div v-show="mobileNavOpen" class="hero-mobile-menu">
                    <button type="button" @click="goToMobileSection('profile')">Profile</button>
                    <button type="button" @click="goToMobileSection('projects')">Projects</button>
                    <button type="button" @click="goToMobileSection('advantages')">Advantages</button>
                    <button type="button" @click="copyText(configdata.contact.wechat); mobileNavOpen = false">Contact</button>
                  </div>
                </transition>
              </div>
            </div>
            <div class="hero-stage__body">
              <div class="hero-stage__copy">
                <div class="hero-stage__eyebrow">AI Tools / Coding / Graduate School</div>
                <h1 class="hero-stage__title">
                  <span>不想平庸</span>
                  <span>LANREN PORTFOLIO</span>
                </h1>
                <p class="hero-stage__intro">
                  好好生活，好好赚钱。用 AI 工具、代码和持续学习，把想法做成能运行、能展示、能成长的作品。
                </p>
                <div class="hero-stage__meta">
                  <span>Vue + Vite</span>
                  <span>Vercel Live</span>
                  <span>NetEase Music</span>
                </div>
                <div class="hero-stage__actions">
                  <v-btn class="hero-stage__action" prepend-icon="mdi-github" :href="configdata.contact.github" target="_blank" variant="tonal">
                    GitHub
                  </v-btn>
                  <v-btn class="hero-stage__action" prepend-icon="mdi-web" :href="configdata.contact.site" target="_blank" variant="tonal">
                    已上线 / Live
                  </v-btn>
                  <v-btn class="hero-stage__action" prepend-icon="mdi-wechat" @click="copyText(configdata.contact.wechat)" variant="tonal">
                    微信
                  </v-btn>
                </div>
              </div>
            </div>
            <div class="hero-stage__bottom">
              <typewriter class="hero-stage__quote"></typewriter>
              <button class="hero-stage__scroll" type="button" @click="scrollToSection('profile')" aria-label="Scroll to profile">
                <span>Scroll</span>
                <v-icon icon="mdi-arrow-down"></v-icon>
              </button>
            </div>
          </section>

          <section
            id="profile"
            class="content-section profile-section"
            @pointermove="handleSectionPointer"
            @pointerleave="resetSectionPointer"
          >
            <div class="profile-fx-layer" aria-hidden="true">
              <span
                v-for="n in 42"
                :key="`profile-particle-${n}`"
                class="profile-particle"
                :style="particleStyle(n)"
              ></span>
            </div>
            <div class="section-heading">
              <span>01 / Profile</span>
              <h2>学习、代码和 AI 工具开发</h2>
              <p>{{ configdata.brandProfile.subtitle }}</p>
            </div>
            <div class="profile-grid">
              <div class="profile-card profile-card--intro">
                <img :src="configdata.avatar" alt="Lanren avatar">
                <div>
                  <h3>{{ configdata.brandProfile.title }}</h3>
                  <p>我现在热爱学习，努力考上研究生，也喜欢把 AI 工具开发变成一个个能展示的作品。</p>
                  <div class="profile-tags">
                    <span v-for="tag in configdata.tags" :key="tag">{{ tag }}</span>
                  </div>
                  <div class="profile-focus-tabs" aria-label="Profile focus">
                    <button
                      v-for="(item,index) in configdata.brandProfile.focus"
                      :key="item.label"
                      type="button"
                      :class="{ 'is-active': activeFocusIndex === index }"
                      @click="setActiveFocus(index)"
                    >
                      <v-icon :icon="item.icon" size="18"></v-icon>
                      <span>{{ item.label }}</span>
                    </button>
                  </div>
                </div>
              </div>
              <div class="profile-card profile-card--chart">
                <polarchart />
              </div>
              <div class="profile-card profile-card--focus">
                <v-icon :icon="activeFocus.icon || 'mdi-compass-outline'" size="32"></v-icon>
                <div>
                  <small>Focus Track</small>
                  <h3>{{ activeFocus.label }}</h3>
                  <p>{{ activeFocus.text }}</p>
                </div>
              </div>
              <button class="profile-card profile-card--quote" type="button" @click="cycleQuote">
                <v-icon icon="mdi-lightbulb-on-outline"></v-icon>
                <span>{{ activeQuote }}</span>
              </button>
            </div>
          </section>

          <section id="projects" class="content-section projects-section">
            <div class="section-heading">
              <span>02 / Selected Projects</span>
              <h2>精选项目</h2>
              <p>用大卡片展示可点击、可上线、可继续迭代的作品。</p>
            </div>
            <div class="project-experience">
              <div class="project-detail" v-if="activeProject.title">
                <div class="project-detail__copy">
                  <span :style="{ color: activeProject.statusColor || 'var(--leleo-vcard-color)' }">{{ activeProject.status }}</span>
                  <h3>{{ activeProject.title }}</h3>
                  <p>{{ activeProject.text }}</p>
                  <div class="project-showcase__actions">
                    <button type="button" @click="openProjectDetail(activeProjectIndex)">详情 / Details</button>
                    <a :href="activeProject.url" target="_blank">{{ activeProject.go }}</a>
                    <a v-if="activeProject.sourceUrl" :href="activeProject.sourceUrl" target="_blank">源码 / Code</a>
                  </div>
                </div>
                <div class="project-detail__preview">
                  <img :src="activeProject.img" :alt="activeProject.title">
                </div>
              </div>
              <div class="project-flow" aria-label="Flowing project cards">
                <div class="project-flow__track">
                  <div class="project-flow__group" v-for="loop in 2" :key="loop">
                    <article
                      v-for="(item,key) in projectcards"
                      :key="`${loop}-${item.title}`"
                      class="project-showcase"
                      :class="{ 'is-active': activeProjectIndex === key }"
                      @mouseenter="setActiveProject(key)"
                      @focusin="setActiveProject(key)"
                    >
                      <button type="button" class="project-showcase__image" @click="openProjectDetail(key)">
                        <img :src="item.img" :alt="item.title">
                        <span :style="{ color: item.statusColor || 'var(--leleo-vcard-color)', borderColor: item.statusColor || 'var(--leleo-vcard-color)' }">
                          {{ item.status }}
                        </span>
                      </button>
                      <div class="project-showcase__body">
                        <p>{{ item.subtitle }}</p>
                        <h3>{{ item.title }}</h3>
                        <div>{{ item.text }}</div>
                        <div class="project-showcase__actions">
                          <button type="button" @click="openProjectDetail(key)">详情 / Details</button>
                          <a :href="item.url" target="_blank">{{ item.go }}</a>
                          <a v-if="item.sourceUrl" :href="item.sourceUrl" target="_blank">源码 / Code</a>
                        </div>
                      </div>
                    </article>
                  </div>
                </div>
              </div>
              <div class="project-dots" aria-label="Project selector">
                <button
                  v-for="(item,index) in projectcards"
                  :key="item.title"
                  type="button"
                  :class="{ 'is-active': activeProjectIndex === index }"
                  @click="setActiveProject(index)"
                  :aria-label="`Show ${item.title}`"
                ></button>
              </div>
            </div>
          </section>

          <section id="advantages" class="content-section advantages-section">
            <div class="section-heading">
              <span>03 / Advantages</span>
              <h2>个人优势</h2>
              <p>现在不是完美状态，但方向清楚：学习、行动、复盘、继续做。</p>
            </div>
            <div class="advantage-grid">
              <div class="advantage-card" v-for="item in configdata.brandProfile.focus" :key="item.label">
                  <v-icon :icon="item.icon" size="22"></v-icon>
                  <div>
                    <h3>{{ item.label }}</h3>
                    <p>{{ item.text }}</p>
                  </div>
              </div>
            </div>
          </section>

          <section id="contact" class="content-section contact-section">
            <div class="contact-panel">
              <div>
                <span>04 / Contact</span>
                <h2>一起把想法做出来</h2>
                <p>欢迎通过 GitHub、网站或微信联系我。微信点击即可复制。</p>
              </div>
              <div class="contact-actions">
                <a :href="configdata.contact.site" target="_blank">lanren.me</a>
                <a :href="configdata.contact.github" target="_blank">GitHub</a>
                <button type="button" @click="copyText(configdata.contact.wechat)">微信 {{ configdata.contact.wechat }}</button>
                <button type="button" @click="copyText(configdata.contact.email)">复制邮箱 / Copy</button>
              </div>
              <transition name="fade">
                <div v-show="copyMessage" class="contact-copy-tip">{{ copyMessage }}</div>
              </transition>
            </div>
          </section>

          <v-dialog
            v-model="projectDialogOpen"
            :width="xs || sm ? 'calc(100vw - 24px)' : 980"
            max-width="980"
            class="project-dialog"
          >
            <div class="project-dialog__panel">
              <button class="project-dialog__close" type="button" aria-label="Close project details" @click="projectDialogOpen = false">
                <v-icon icon="mdi-close" size="20"></v-icon>
              </button>
              <div class="project-dialog__media">
                <img :src="projectDetail.img" :alt="projectDetail.title">
                <span :style="{ color: projectDetail.statusColor || 'var(--leleo-vcard-color)', borderColor: projectDetail.statusColor || 'var(--leleo-vcard-color)' }">
                  {{ projectDetail.status }}
                </span>
              </div>
              <div class="project-dialog__copy">
                <small>Project Detail / 项目详情</small>
                <h3>{{ projectDetail.title }}</h3>
                <p>{{ projectDetail.text }}</p>
                <div class="project-dialog__stack">
                  <span v-for="item in projectDetailStack" :key="item">{{ item }}</span>
                </div>
                <div class="project-dialog__points">
                  <div v-for="point in projectDetailPoints" :key="point.title">
                    <v-icon :icon="point.icon" size="20"></v-icon>
                    <span>
                      <strong>{{ point.title }}</strong>
                      <small>{{ point.text }}</small>
                    </span>
                  </div>
                </div>
                <div class="project-dialog__actions">
                  <a :href="projectDetail.url" target="_blank">{{ projectDetail.go || 'Visit' }}</a>
                  <a v-if="projectDetail.sourceUrl" :href="projectDetail.sourceUrl" target="_blank">源码 / Code</a>
                  <button type="button" @click="projectDialogOpen = false">关闭 / Close</button>
                </div>
              </div>
            </div>
          </v-dialog>

          <transition name="fade">
            <div v-if="commandPaletteOpen" class="command-overlay" @click.self="closeCommandPalette">
              <div class="command-palette" role="dialog" aria-modal="true" aria-label="Command palette">
                <div class="command-palette__search">
                  <v-icon icon="mdi-magnify" size="22"></v-icon>
                  <input
                    ref="commandInput"
                    v-model="commandQuery"
                    type="text"
                    placeholder="搜索项目、页面或输入关键词 / Search commands"
                    @keydown.enter.prevent="runFirstCommand"
                    @keydown.esc.prevent="closeCommandPalette"
                  >
                  <button type="button" @click="closeCommandPalette">
                    <v-icon icon="mdi-close" size="18"></v-icon>
                  </button>
                </div>
                <div class="command-palette__list">
                  <button
                    v-for="item in commandItems"
                    :key="`${item.type}-${item.title}`"
                    type="button"
                    @click="runCommand(item)"
                  >
                    <v-icon :icon="item.icon" size="22"></v-icon>
                    <span>
                      <strong>{{ item.title }}</strong>
                      <small>{{ item.subtitle }}</small>
                    </span>
                    <em>{{ item.badge }}</em>
                  </button>
                  <div v-if="!commandItems.length" class="command-palette__empty">
                    没有匹配结果 / No result
                  </div>
                </div>
              </div>
            </div>
          </transition>
      </main>
</template> 

<script>
import typewriter from '../components/typewriter.vue';
import polarchart from '../components/polarchart.vue';
import { useDisplay } from 'vuetify'

export default {
    components: {
        typewriter,polarchart
    },
    props: ['configdata','formattedTime','formattedDate','projectcards'],
	data() {
		return {
			quoteIndex: 0,
			activeFocusIndex: 0,
			activeProjectIndex: 0,
			copyMessage: '',
			copyTimer: null,
			searchQuery: '',
			searchEngineMenuOpen: false,
			selectedEngine: { title: 'Bing', value: 'bing' },
			searchEngines: [
				{ title: 'Bing', value: 'bing' },
				{ title: 'Google', value: 'google' },
				{ title: '百度 Baidu', value: 'baidu' },
				{ title: 'Yandex', value: 'yandex' },
				{ title: 'DuckDuckGo', value: 'duckduckgo' },
			],
			projectDialogOpen: false,
			projectDetailIndex: 0,
			mobileNavOpen: false,
			commandPaletteOpen: false,
			commandQuery: '',
		}
	},
    setup() {
      const { xs,sm,md } = useDisplay();
      return {xs,sm,md};
    },
	computed: {	
		activeQuote(){
			const quotes = this.configdata.brandProfile?.quotes || [];
			return quotes[this.quoteIndex % quotes.length] || "继续向前 / Keep building";
		},
		activeFocus(){
			const focus = this.configdata.brandProfile?.focus || [];
			return focus[this.activeFocusIndex] || {};
		},
		activeProject(){
			const cards = this.projectcards || [];
			return cards[this.activeProjectIndex] || {};
		},
		isUrl(){
			return this.isLikelyUrl(this.searchQuery);
		},
		projectDetail(){
			const cards = this.projectcards || [];
			return cards[this.projectDetailIndex] || cards[this.activeProjectIndex] || {};
		},
		projectDetailStack(){
			const subtitle = this.projectDetail?.subtitle || '';
			const parts = subtitle.split('/').map(item => item.trim()).filter(Boolean);
			return parts.length ? parts : ['Vue', 'GitHub', 'AI Tools'];
		},
		projectDetailPoints(){
			const status = this.projectDetail?.status || 'Building';
			const sourceText = this.projectDetail?.sourceUrl
				? '支持查看源码，适合放进作品集继续打磨。'
				: '保留访问入口，适合作为个人品牌的一部分。';
			return [
				{ icon: 'mdi-target', title: '目标 / Goal', text: '把想法做成可运行、可展示、可继续迭代的作品。' },
				{ icon: 'mdi-progress-check', title: '状态 / Status', text: status },
				{ icon: 'mdi-source-branch', title: '价值 / Value', text: sourceText },
			];
		},
		commandItems(){
			const query = this.commandQuery.trim().toLowerCase();
			const baseItems = [
				{ type: 'section', target: 'profile', icon: 'mdi-account-outline', title: 'Profile / 个人介绍', subtitle: '学习、AI 工具开发和个人方向', badge: 'Go' },
				{ type: 'section', target: 'projects', icon: 'mdi-folder-star-outline', title: 'Projects / 项目作品', subtitle: '查看已上线和开发中的项目', badge: 'Go' },
				{ type: 'section', target: 'advantages', icon: 'mdi-star-four-points-outline', title: 'Advantages / 个人优势', subtitle: '查看当前目标和能力方向', badge: 'Go' },
				{ type: 'copy', target: this.configdata.contact.wechat, icon: 'mdi-wechat', title: '复制微信 / Copy WeChat', subtitle: this.configdata.contact.wechat, badge: 'Copy' },
				{ type: 'external', target: this.configdata.contact.github, icon: 'mdi-github', title: 'GitHub / Sunnyleo-debug', subtitle: '打开 GitHub 主页', badge: 'Open' },
				...((this.projectcards || []).map((project, index) => ({
					type: 'project',
					target: index,
					icon: 'mdi-cube-outline',
					title: project.title,
					subtitle: project.subtitle || project.text,
					badge: 'Detail',
				}))),
			];

			if (!query) return baseItems;

			const matchedItems = baseItems.filter((item) => {
				return `${item.title} ${item.subtitle} ${item.badge}`.toLowerCase().includes(query);
			});

			return [
				...matchedItems,
				{
					type: 'search',
					target: this.commandQuery.trim(),
					icon: this.isLikelyUrl(this.commandQuery) ? 'mdi-earth' : 'mdi-magnify',
					title: this.isLikelyUrl(this.commandQuery) ? `打开 ${this.commandQuery.trim()}` : `搜索 ${this.commandQuery.trim()}`,
					subtitle: this.isLikelyUrl(this.commandQuery) ? '直接打开网址 / Open URL' : `${this.selectedEngine.title} Search`,
					badge: matchedItems.length ? 'Web' : 'Enter',
				},
			];
		},
	},
	mounted(){
		window.addEventListener('keydown', this.handleGlobalKeydown);
	},
	beforeUnmount(){
		window.removeEventListener('keydown', this.handleGlobalKeydown);
    },
    methods:{
		handleGlobalKeydown(event){
			const key = event.key?.toLowerCase();
			if ((event.metaKey || event.ctrlKey) && key === 'k') {
				event.preventDefault();
				this.openCommandPalette();
				return;
			}

			if (event.key === 'Escape') {
				this.searchEngineMenuOpen = false;
				this.mobileNavOpen = false;
				if (this.commandPaletteOpen) this.closeCommandPalette();
			}
		},
		openCommandPalette(seed = ''){
			this.commandQuery = seed;
			this.commandPaletteOpen = true;
			this.mobileNavOpen = false;
			this.searchEngineMenuOpen = false;
			this.$nextTick(() => {
				this.$refs.commandInput?.focus?.();
			});
		},
		closeCommandPalette(){
			this.commandPaletteOpen = false;
			this.commandQuery = '';
		},
		runFirstCommand(){
			if (!this.commandItems.length) return;
			this.runCommand(this.commandItems[0]);
		},
		runCommand(item){
			if (!item) return;

			if (item.type === 'section') {
				this.closeCommandPalette();
				this.scrollToSection(item.target);
				return;
			}

			if (item.type === 'project') {
				this.closeCommandPalette();
				this.openProjectDetail(item.target);
				return;
			}

			if (item.type === 'copy') {
				this.closeCommandPalette();
				this.copyText(item.target);
				return;
			}

			if (item.type === 'external') {
				this.closeCommandPalette();
				window.open(item.target, '_blank', 'noopener,noreferrer');
				return;
			}

			if (item.type === 'search') {
				this.searchQuery = item.target;
				this.closeCommandPalette();
				this.performSearch();
			}
		},
		openProjectDetail(index){
			const length = (this.projectcards || []).length;
			if (!length) return;
			this.projectDetailIndex = index % length;
			this.activeProjectIndex = this.projectDetailIndex;
			this.projectDialogOpen = true;
		},
		toggleMobileNav(){
			this.mobileNavOpen = !this.mobileNavOpen;
			this.searchEngineMenuOpen = false;
		},
		goToMobileSection(id){
			this.mobileNavOpen = false;
			this.$nextTick(() => {
				this.scrollToSection(id);
			});
		},
		selectSearchEngine(engine){
			this.selectedEngine = engine;
			this.searchEngineMenuOpen = false;
		},
		performSearch(){
			const query = this.searchQuery.trim();
			if (!query) return;

			if (this.isUrl) {
				const url = /^[a-z]+:\/\//i.test(query) ? query : `https://${query}`;
				window.open(url, '_blank', 'noopener,noreferrer');
				return;
			}

			const engineUrls = {
				google: `https://www.google.com/search?q=${encodeURIComponent(query)}`,
				bing: `https://www.bing.com/search?q=${encodeURIComponent(query)}`,
				baidu: `https://www.baidu.com/s?wd=${encodeURIComponent(query)}`,
				yandex: `https://yandex.com/search/?text=${encodeURIComponent(query)}`,
				duckduckgo: `https://duckduckgo.com/?q=${encodeURIComponent(query)}`,
			};
			window.open(engineUrls[this.selectedEngine.value] || engineUrls.bing, '_blank', 'noopener,noreferrer');
			this.searchEngineMenuOpen = false;
		},
		isLikelyUrl(input){
			const value = String(input || '').trim();
			if (!value || /\s/.test(value)) return false;
			if (/^(https?|ftp):\/\//i.test(value)) return true;

			const domainPattern = /^([a-z0-9-]+\.)+[a-z]{2,}(\/.*)?$/i;
			const localPattern = /^(localhost|\d{1,3}(\.\d{1,3}){3})(:\d+)?(\/.*)?$/i;
			return domainPattern.test(value) || localPattern.test(value);
		},
		setActiveFocus(index){
			this.activeFocusIndex = index;
		},
		setActiveProject(index){
			const length = (this.projectcards || []).length;
			if (!length) return;
			this.activeProjectIndex = index % length;
		},
		handleSectionPointer(event){
			const target = event.currentTarget;
			const rect = target.getBoundingClientRect();
			const activeCard = event.target.closest?.('.profile-card');
			const previousCard = target.querySelector('.profile-card.is-card-active');

			target.style.setProperty('--fx-x', `${event.clientX - rect.left}px`);
			target.style.setProperty('--fx-y', `${event.clientY - rect.top}px`);
			target.classList.add('is-pointer-active');

			if (previousCard && previousCard !== activeCard) {
				previousCard.classList.remove('is-card-active');
			}

			if (activeCard) {
				activeCard.classList.add('is-card-active');
			}
		},
		resetSectionPointer(event){
			const target = event.currentTarget;
			target.classList.remove('is-pointer-active');
			target.querySelectorAll('.profile-card.is-card-active').forEach((card) => {
				card.classList.remove('is-card-active');
			});
		},
		particleStyle(index){
			const x = (index * 37) % 100;
			const y = (index * 53) % 100;
			const size = 2 + (index % 4);
			const duration = 7 + (index % 6) * 1.4;
			const delay = (index % 9) * -0.55;

			return {
				'--particle-x': `${x}%`,
				'--particle-y': `${y}%`,
				'--particle-size': `${size}px`,
				'--particle-duration': `${duration}s`,
				'--particle-delay': `${delay}s`,
			};
		},
		scrollToSection(id){
			const target = document.getElementById(id);
			if (!target) return;

			const shell = document.querySelector('.site-shell') || document.scrollingElement || document.documentElement;
			const shellRect = shell === document.scrollingElement || shell === document.documentElement
				? { top: 0 }
				: shell.getBoundingClientRect();
			const targetRect = target.getBoundingClientRect();
			const currentTop = shell.scrollTop || window.scrollY || 0;
			const top = currentTop + targetRect.top - shellRect.top;

			if (typeof shell.scrollTo === 'function') {
				shell.scrollTo({
					top,
					behavior: 'smooth',
				});
				return;
			}

			window.scrollTo({
				top,
				behavior: 'smooth',
			});
		},
		cycleQuote(){
			const quotes = this.configdata.brandProfile?.quotes || [];
			if(!quotes.length) return;
			this.quoteIndex = (this.quoteIndex + 1) % quotes.length;
		},
		async copyText(text){
			try {
				await navigator.clipboard.writeText(text);
			} catch (error) {
				const input = document.createElement('input');
				input.value = text;
				document.body.appendChild(input);
				input.select();
				document.execCommand('copy');
				document.body.removeChild(input);
			}
			this.copyMessage = `已复制 ${text} / Copied`;
			clearTimeout(this.copyTimer);
			this.copyTimer = setTimeout(() => {
				this.copyMessage = '';
			}, 1600);
		}
    }
};
</script>

<style scoped>
@import url(/css/app.less);

.portfolio-page {
	min-height: 100vh;
	color: #fff;
	scroll-behavior: smooth;
}

.hero-stage {
	position: relative;
	min-height: 100vh;
	box-sizing: border-box;
	padding: 28px clamp(18px, 5vw, 72px);
	display: flex;
	flex-direction: column;
	justify-content: space-between;
	overflow: hidden;
	background:
		linear-gradient(90deg, rgba(5, 10, 22, 0.64), rgba(5, 10, 22, 0.2) 54%, rgba(5, 10, 22, 0.58)),
		radial-gradient(circle at 76% 20%, rgba(124, 255, 178, 0.2), transparent 28%),
		radial-gradient(circle at 20% 72%, rgba(0, 229, 255, 0.18), transparent 32%);
}

.hero-stage::before {
	content: "";
	position: absolute;
	inset: 0;
	background-image:
		linear-gradient(rgba(255, 255, 255, 0.045) 1px, transparent 1px),
		linear-gradient(90deg, rgba(255, 255, 255, 0.045) 1px, transparent 1px);
	background-size: 48px 48px;
	mask-image: linear-gradient(to bottom, rgba(0,0,0,0.8), transparent 88%);
	pointer-events: none;
}

.hero-stage::after {
	content: "";
	position: absolute;
	left: 0;
	right: 0;
	bottom: -1px;
	height: 140px;
	background: linear-gradient(to bottom, transparent, rgba(5, 10, 22, 0.72));
	pointer-events: none;
}

.hero-stage > * {
	position: relative;
	z-index: 1;
}

.hero-stage__topline {
	position: relative;
	z-index: 30;
	display: grid;
	grid-template-columns: minmax(118px, 1fr) minmax(250px, 330px) minmax(330px, 1fr);
	align-items: center;
	gap: 16px;
}

.hero-stage__brand {
	display: flex;
	align-items: baseline;
	gap: 10px;
	font-weight: 900;
	letter-spacing: 0;
}

.hero-stage__brand span {
	font-size: 20px;
}

.hero-stage__brand small {
	color: rgba(221, 247, 255, 0.62);
	font-size: 12px;
	font-weight: 800;
}

.hero-search {
	position: relative;
	display: grid;
	grid-template-columns: auto minmax(0, 1fr) auto;
	align-items: center;
	width: 100%;
	min-height: 40px;
	padding: 4px;
	border: 1px solid rgba(221, 247, 255, 0.1);
	border-radius: 999px;
	background:
		linear-gradient(135deg, rgba(255, 255, 255, 0.055), rgba(0, 229, 255, 0.02)),
		rgba(6, 12, 24, 0.18);
	backdrop-filter: blur(12px);
	box-shadow:
		0 10px 28px rgba(0, 0, 0, 0.12),
		inset 0 1px 0 rgba(255, 255, 255, 0.08);
}

.hero-search--top {
	justify-self: center;
	max-width: 330px;
}

.hero-search__engine-wrap {
	position: relative;
}

.hero-search__engine,
.hero-search__submit,
.hero-search__menu button {
	border: 0;
	font: inherit;
	cursor: pointer;
}

.hero-search__engine,
.hero-search__submit {
	display: inline-flex;
	align-items: center;
	justify-content: center;
	gap: 6px;
	min-height: 32px;
	border-radius: 999px;
	color: rgba(255, 255, 255, 0.88);
	font-size: 11px;
	font-weight: 900;
	white-space: nowrap;
	transition: background 0.2s ease, color 0.2s ease, transform 0.2s ease;
}

.hero-search__engine {
	padding: 0 8px 0 10px;
	background: rgba(255, 255, 255, 0.045);
}

.hero-search__submit {
	width: 32px;
	padding: 0;
	background: rgba(124, 255, 178, 0.1);
	color: #dfffea;
}

.hero-search__engine:hover,
.hero-search__submit:hover {
	background: rgba(124, 255, 178, 0.18);
	color: #fff;
	transform: translateY(-1px);
}

.hero-search input {
	width: 100%;
	min-width: 0;
	height: 32px;
	padding: 0 8px;
	border: 0;
	outline: 0;
	background: transparent;
	color: #fff;
	font-size: 12px;
	font-weight: 800;
}

.hero-search input::placeholder {
	color: rgba(221, 247, 255, 0.5);
}

.hero-search__menu {
	position: absolute;
	top: calc(100% + 10px);
	right: 0;
	z-index: 20;
	width: 168px;
	padding: 8px;
	border: 1px solid rgba(221, 247, 255, 0.16);
	border-radius: 16px;
	background: rgba(6, 12, 24, 0.86);
	backdrop-filter: blur(18px);
	box-shadow: 0 18px 46px rgba(0, 0, 0, 0.32);
}

.hero-search__menu button {
	display: block;
	width: 100%;
	padding: 9px 10px;
	border-radius: 10px;
	background: transparent;
	color: rgba(255, 255, 255, 0.76);
	font-size: 12px;
	font-weight: 850;
	text-align: left;
	transition: background 0.2s ease, color 0.2s ease;
}

.hero-search__menu button:hover,
.hero-search__menu button.is-active {
	background: rgba(124, 255, 178, 0.12);
	color: #fff;
}

.hero-stage__links {
	display: flex;
	align-items: center;
	justify-self: end;
	gap: 6px;
	padding: 6px;
	border: 1px solid rgba(221, 247, 255, 0.14);
	border-radius: 999px;
	background: rgba(6, 12, 24, 0.28);
	backdrop-filter: blur(18px);
}

.hero-stage__nav {
	position: relative;
	z-index: 31;
	justify-self: end;
	display: flex;
	align-items: center;
}

.hero-menu-toggle,
.hero-mobile-menu {
	display: none;
}

.hero-menu-toggle {
	width: 42px;
	height: 42px;
	border: 1px solid rgba(221, 247, 255, 0.16);
	border-radius: 999px;
	background: rgba(6, 12, 24, 0.38);
	color: rgba(255, 255, 255, 0.9);
	backdrop-filter: blur(18px);
	cursor: pointer;
}

.hero-mobile-menu {
	position: absolute;
	top: calc(100% + 10px);
	right: 0;
	z-index: 40;
	width: 220px;
	padding: 10px;
	border: 1px solid rgba(221, 247, 255, 0.16);
	border-radius: 18px;
	background: rgba(6, 12, 24, 0.82);
	backdrop-filter: blur(18px);
	box-shadow: 0 18px 46px rgba(0, 0, 0, 0.32);
}

.hero-mobile-menu button {
	display: flex;
	width: 100%;
	padding: 11px 12px;
	border: 0;
	border-radius: 12px;
	background: transparent;
	color: rgba(255, 255, 255, 0.84);
	cursor: pointer;
	font-size: 13px;
	font-weight: 900;
	text-align: left;
}

.hero-mobile-menu button:hover {
	background: rgba(124, 255, 178, 0.12);
	color: #fff;
}

.hero-stage__links a,
.hero-stage__links button,
.contact-actions a,
.contact-actions button,
.project-showcase__actions a {
	color: rgba(255, 255, 255, 0.86);
	text-decoration: none;
	border: 0;
	background: transparent;
	cursor: pointer;
	font: inherit;
}

.hero-stage__links a,
.hero-stage__links button {
	padding: 7px 9px;
	border-radius: 999px;
	font-size: 12px;
	font-weight: 800;
	transition: background 0.25s ease, color 0.25s ease;
}

.hero-stage__links a:hover,
.hero-stage__links button:hover {
	background: rgba(221, 247, 255, 0.12);
	color: #fff;
}

.hero-stage__body {
	display: flex;
	align-items: center;
	min-height: calc(100vh - 220px);
	max-width: 980px;
	padding: 26px 0 20px;
}

.hero-stage__copy {
	max-width: 880px;
}

.hero-stage__eyebrow {
	color: #7CFFB2;
	font-size: 13px;
	font-weight: 900;
	margin-bottom: 14px;
}

.hero-stage__title {
	margin: 0;
	display: flex;
	flex-direction: column;
	gap: 2px;
	font-size: 88px;
	line-height: 0.86;
	font-weight: 950;
	letter-spacing: 0;
	text-transform: uppercase;
}

.hero-stage__title span:first-child {
	font-size: 64px;
}

.hero-stage__title span:last-child {
	background: linear-gradient(95deg, #fff 0%, #DDF7FF 32%, #7CFFB2 68%, #00E5FF 100%);
	-webkit-background-clip: text;
	background-clip: text;
	color: transparent;
	text-shadow: 0 18px 60px rgba(0, 229, 255, 0.16);
}

.hero-stage__intro {
	max-width: 660px;
	margin: 24px 0 0;
	color: rgba(255, 255, 255, 0.78);
	font-size: 19px;
	line-height: 1.7;
	font-weight: 700;
}

.hero-stage__meta,
.profile-tags {
	display: flex;
	flex-wrap: wrap;
	gap: 10px;
	margin-top: 22px;
}

.hero-stage__meta span,
.profile-tags span {
	padding: 7px 11px;
	border: 1px solid rgba(221, 247, 255, 0.16);
	border-radius: 999px;
	background: rgba(255, 255, 255, 0.06);
	color: rgba(221, 247, 255, 0.86);
	font-size: 12px;
	font-weight: 800;
}

.hero-stage__actions {
	display: flex;
	flex-wrap: wrap;
	gap: 12px;
	margin-top: 28px;
}

.hero-stage__action {
	border: 1px solid rgba(221, 247, 255, 0.22);
}

.hero-stage__bottom {
	display: flex;
	align-items: flex-end;
	justify-content: space-between;
	gap: 18px;
	min-height: 56px;
}

.hero-stage__quote {
	max-width: 760px;
}

.hero-stage__scroll {
	display: inline-flex;
	align-items: center;
	gap: 8px;
	border: 0;
	background: transparent;
	color: rgba(255, 255, 255, 0.74);
	cursor: pointer;
	font-size: 12px;
	font-weight: 900;
	animation: scroll-pulse 1.5s ease-in-out infinite;
}

.content-section {
	position: relative;
	padding: clamp(72px, 9vw, 120px) clamp(18px, 5vw, 72px);
	overflow: hidden;
	background:
		linear-gradient(180deg, rgba(5, 10, 22, 0.62), rgba(5, 10, 22, 0.78)),
		radial-gradient(circle at 12% 18%, rgba(124, 255, 178, 0.08), transparent 30%),
		radial-gradient(circle at 84% 10%, rgba(0, 229, 255, 0.08), transparent 32%);
}

.content-section::before {
	content: "";
	position: absolute;
	inset: 0;
	z-index: 0;
	background-image:
		linear-gradient(rgba(255, 255, 255, 0.026) 1px, transparent 1px),
		linear-gradient(90deg, rgba(255, 255, 255, 0.026) 1px, transparent 1px);
	background-size: 56px 56px;
	mask-image: linear-gradient(180deg, transparent, #000 12%, #000 86%, transparent);
	opacity: 0.45;
	pointer-events: none;
}

.content-section > * {
	position: relative;
	z-index: 1;
}

.profile-section {
	--fx-x: 50%;
	--fx-y: 34%;
}

.profile-section::after {
	content: "";
	position: absolute;
	left: var(--fx-x);
	top: var(--fx-y);
	z-index: 0;
	width: min(520px, 58vw);
	aspect-ratio: 1;
	border-radius: 999px;
	background:
		radial-gradient(circle, rgba(0, 229, 255, 0.22), rgba(124, 255, 178, 0.1) 32%, transparent 68%);
	filter: blur(20px);
	mix-blend-mode: screen;
	opacity: 0;
	transform: translate(-50%, -50%) scale(0.78);
	transition: opacity 0.25s ease, transform 0.35s ease;
	pointer-events: none;
}

.profile-section.is-pointer-active::after {
	opacity: 0.9;
	transform: translate(-50%, -50%) scale(1);
}

.profile-fx-layer {
	position: absolute;
	inset: 0;
	z-index: 0;
	overflow: hidden;
	pointer-events: none;
}

.profile-particle {
	position: absolute;
	left: var(--particle-x);
	top: var(--particle-y);
	width: var(--particle-size);
	height: var(--particle-size);
	border-radius: 999px;
	background: rgba(221, 247, 255, 0.9);
	box-shadow:
		0 0 10px rgba(0, 229, 255, 0.9),
		0 0 24px rgba(124, 255, 178, 0.42);
	opacity: 0.18;
	animation: profile-particle-drift var(--particle-duration) ease-in-out infinite;
	animation-delay: var(--particle-delay);
}

.profile-particle:nth-child(3n) {
	background: rgba(124, 255, 178, 0.95);
	box-shadow:
		0 0 12px rgba(124, 255, 178, 0.95),
		0 0 30px rgba(0, 229, 255, 0.35);
}

.profile-particle:nth-child(4n) {
	background: rgba(0, 229, 255, 0.88);
	transform-origin: center;
}

.section-heading {
	max-width: 760px;
	margin-bottom: 34px;
}

.section-heading span,
.contact-panel span {
	color: #7CFFB2;
	font-size: 13px;
	font-weight: 900;
}

.section-heading h2,
.contact-panel h2 {
	margin: 8px 0 0;
	color: #fff;
	font-size: 56px;
	line-height: 1;
	font-weight: 950;
	letter-spacing: 0;
}

.section-heading p,
.contact-panel p {
	margin: 14px 0 0;
	color: rgba(255, 255, 255, 0.68);
	font-size: 16px;
	line-height: 1.7;
}

.profile-grid {
	display: grid;
	grid-template-columns: minmax(0, 1.35fr) minmax(260px, 0.75fr);
	gap: 18px;
}

.profile-card,
.advantage-card,
.contact-panel,
.project-showcase {
	position: relative;
	border: 1px solid rgba(221, 247, 255, 0.14);
	border-radius: 20px;
	background: linear-gradient(145deg, rgba(255, 255, 255, 0.1), rgba(255, 255, 255, 0.035));
	backdrop-filter: blur(18px);
	box-shadow: 0 22px 60px rgba(0, 0, 0, 0.2);
	overflow: hidden;
}

.profile-card {
	transition: border-color 0.3s ease, box-shadow 0.3s ease, transform 0.3s ease, background 0.3s ease;
}

.profile-card::before {
	content: "";
	position: absolute;
	inset: -1px;
	z-index: 0;
	background:
		radial-gradient(circle at 18% 12%, rgba(124, 255, 178, 0.16), transparent 28%),
		linear-gradient(120deg, transparent 0%, rgba(0, 229, 255, 0.12) 44%, transparent 72%);
	opacity: 0;
	transition: opacity 0.3s ease, transform 0.45s ease;
	transform: translateX(-10%);
	pointer-events: none;
}

.profile-card > * {
	position: relative;
	z-index: 1;
}

.profile-card:hover,
.profile-card:focus-within,
.profile-card.is-card-active {
	border-color: rgba(124, 255, 178, 0.46);
	background:
		linear-gradient(145deg, rgba(255, 255, 255, 0.13), rgba(0, 229, 255, 0.055));
	box-shadow:
		0 30px 90px rgba(0, 0, 0, 0.36),
		0 0 0 1px rgba(124, 255, 178, 0.1),
		0 0 42px rgba(0, 229, 255, 0.1);
	transform: translateY(-6px);
}

.profile-card:hover::before,
.profile-card:focus-within::before,
.profile-card.is-card-active::before {
	opacity: 1;
	transform: translateX(12%);
}

.profile-card--intro {
	grid-row: span 3;
	display: grid;
	grid-template-columns: 160px minmax(0, 1fr);
	gap: 24px;
	align-items: center;
	padding: clamp(22px, 4vw, 42px);
}

.profile-card--intro img {
	width: 160px;
	height: 160px;
	border-radius: 28px;
	object-fit: cover;
	border: 1px solid rgba(221, 247, 255, 0.22);
}

.profile-card h3,
.advantage-card h3,
.project-showcase h3 {
	margin: 0;
	color: #fff;
	font-size: 30px;
	line-height: 1.15;
	font-weight: 950;
}

.profile-card p,
.advantage-card p,
.project-showcase__body div {
	margin: 14px 0 0;
	color: rgba(255, 255, 255, 0.7);
	line-height: 1.7;
}

.profile-focus-tabs {
	display: flex;
	flex-wrap: wrap;
	gap: 10px;
	margin-top: 22px;
}

.profile-focus-tabs button {
	display: inline-flex;
	align-items: center;
	gap: 7px;
	padding: 9px 12px;
	border: 1px solid rgba(221, 247, 255, 0.16);
	border-radius: 999px;
	background: rgba(255, 255, 255, 0.055);
	color: rgba(255, 255, 255, 0.78);
	cursor: pointer;
	font-size: 12px;
	font-weight: 900;
	transition: border-color 0.25s ease, background 0.25s ease, transform 0.25s ease;
}

.profile-focus-tabs button:hover,
.profile-focus-tabs button.is-active {
	border-color: rgba(124, 255, 178, 0.55);
	background: rgba(124, 255, 178, 0.12);
	color: #fff;
	transform: translateY(-2px);
}

.profile-card--chart {
	display: flex;
	align-items: center;
	justify-content: center;
	min-height: 300px;
	padding: 18px;
}

.profile-card--chart::after {
	content: "";
	position: absolute;
	inset: 8%;
	z-index: 0;
	border-radius: 50%;
	border: 1px solid rgba(0, 229, 255, 0.18);
	background:
		conic-gradient(from 90deg, transparent, rgba(0, 229, 255, 0.16), transparent 34%, rgba(124, 255, 178, 0.14), transparent 70%);
	filter: drop-shadow(0 0 22px rgba(0, 229, 255, 0.16));
	animation: profile-orbit 16s linear infinite;
	pointer-events: none;
}

.profile-card--quote {
	min-height: 118px;
	display: flex;
	align-items: center;
	gap: 12px;
	padding: 22px;
	color: #fff;
	text-align: left;
	cursor: pointer;
}

.profile-card--focus {
	min-height: 156px;
	display: flex;
	align-items: flex-start;
	gap: 16px;
	padding: 24px;
}

.profile-card--focus .v-icon {
	color: #7CFFB2;
	filter: drop-shadow(0 0 18px rgba(124, 255, 178, 0.34));
}

.profile-card--focus small {
	display: block;
	margin-bottom: 7px;
	color: rgba(124, 255, 178, 0.86);
	font-size: 11px;
	font-weight: 900;
	text-transform: uppercase;
}

.project-experience {
	display: grid;
	gap: 20px;
}

.project-detail {
	position: relative;
	display: grid;
	grid-template-columns: minmax(0, 1.05fr) minmax(320px, 0.85fr);
	min-height: 320px;
	border: 1px solid rgba(221, 247, 255, 0.16);
	border-radius: 22px;
	background:
		linear-gradient(135deg, rgba(255, 255, 255, 0.12), rgba(255, 255, 255, 0.035)),
		rgba(7, 13, 26, 0.46);
	backdrop-filter: blur(18px);
	box-shadow: 0 26px 72px rgba(0, 0, 0, 0.24);
	overflow: hidden;
}

.project-detail::before {
	content: "";
	position: absolute;
	inset: 0;
	background:
		linear-gradient(90deg, rgba(124, 255, 178, 0.08), transparent 44%),
		linear-gradient(180deg, rgba(0, 229, 255, 0.08), transparent 46%);
	pointer-events: none;
}

.project-detail__copy,
.project-detail__preview {
	position: relative;
	z-index: 1;
}

.project-detail__copy {
	display: flex;
	flex-direction: column;
	justify-content: center;
	padding: clamp(24px, 4vw, 42px);
}

.project-detail__copy > span {
	font-size: 12px;
	font-weight: 950;
	text-transform: uppercase;
}

.project-detail__copy h3 {
	margin: 10px 0 0;
	color: #fff;
	font-size: 42px;
	line-height: 1;
	font-weight: 950;
	letter-spacing: 0;
}

.project-detail__copy p {
	margin: 18px 0 0;
	max-width: 680px;
	color: rgba(255, 255, 255, 0.72);
	font-size: 16px;
	line-height: 1.75;
}

.project-detail__preview {
	min-height: 280px;
	overflow: hidden;
}

.project-detail__preview img {
	width: 100%;
	height: 100%;
	object-fit: cover;
	display: block;
	transform: scale(1.02);
}

.project-detail__preview::after {
	content: "";
	position: absolute;
	inset: 0;
	background: linear-gradient(90deg, rgba(5, 10, 22, 0.78), transparent 40%);
	pointer-events: none;
}

.project-flow {
	position: relative;
	margin: 0 calc(clamp(18px, 5vw, 72px) * -1);
	padding: 8px clamp(18px, 5vw, 72px) 16px;
	overflow: hidden;
	mask-image: linear-gradient(90deg, transparent, #000 8%, #000 92%, transparent);
}

.project-flow__track {
	display: flex;
	width: max-content;
	animation: project-marquee 42s linear infinite;
	will-change: transform;
}

.project-flow__group {
	display: flex;
	gap: 18px;
	padding-right: 18px;
}

.project-flow:hover .project-flow__track,
.project-flow:focus-within .project-flow__track {
	animation-play-state: paused;
}

.project-showcase__image {
	position: relative;
	display: block;
	width: 100%;
	height: 230px;
	overflow: hidden;
	padding: 0;
	border: 0;
	background: transparent;
	cursor: pointer;
	color: inherit;
}

.project-showcase__image img {
	width: 100%;
	height: 100%;
	object-fit: cover;
	display: block;
	transition: transform 0.55s ease;
}

.project-showcase:hover .project-showcase__image img {
	transform: scale(1.06);
}

.project-showcase {
	flex: 0 0 370px;
	width: 370px;
	cursor: default;
	transition: border-color 0.3s ease, transform 0.3s ease, box-shadow 0.3s ease;
}

.project-showcase::before {
	content: "";
	position: absolute;
	inset: 0;
	background: linear-gradient(115deg, transparent 0%, rgba(255, 255, 255, 0.13) 45%, transparent 70%);
	transform: translateX(-120%);
	transition: transform 0.65s ease;
	pointer-events: none;
	z-index: 2;
}

.project-showcase:hover,
.project-showcase.is-active {
	border-color: rgba(124, 255, 178, 0.45);
	box-shadow: 0 26px 80px rgba(0, 0, 0, 0.34), 0 0 0 1px rgba(124, 255, 178, 0.08);
	transform: translateY(-6px);
}

.project-showcase:hover::before,
.project-showcase.is-active::before {
	transform: translateX(120%);
}

.project-showcase__image span {
	position: absolute;
	top: 14px;
	right: 14px;
	padding: 5px 9px;
	border: 1px solid;
	border-radius: 999px;
	background: rgba(8, 14, 28, 0.66);
	backdrop-filter: blur(10px);
	font-size: 12px;
	font-weight: 900;
}

.project-showcase__body {
	padding: 20px;
	min-height: 248px;
}

.project-showcase__body p {
	margin: 0 0 8px;
	color: #7CFFB2;
	font-size: 12px;
	font-weight: 900;
}

.project-showcase__actions {
	display: flex;
	flex-wrap: wrap;
	gap: 10px;
	margin-top: 18px;
}

.project-dots {
	display: flex;
	justify-content: center;
	gap: 8px;
}

.project-dots button {
	width: 28px;
	height: 6px;
	border: 0;
	border-radius: 999px;
	background: rgba(221, 247, 255, 0.22);
	cursor: pointer;
	transition: width 0.25s ease, background 0.25s ease;
}

.project-dots button.is-active {
	width: 48px;
	background: #7CFFB2;
}

.project-showcase__actions a,
.project-showcase__actions button,
.contact-actions a,
.contact-actions button {
	padding: 9px 12px;
	border: 1px solid rgba(221, 247, 255, 0.16);
	border-radius: 999px;
	background: rgba(255, 255, 255, 0.06);
	color: rgba(255, 255, 255, 0.86);
	cursor: pointer;
	font: inherit;
	font-size: 13px;
	font-weight: 900;
	text-decoration: none;
	transition: background 0.25s ease, transform 0.25s ease;
}

.project-showcase__actions a:hover,
.project-showcase__actions button:hover,
.contact-actions a:hover,
.contact-actions button:hover {
	background: rgba(124, 255, 178, 0.12);
	transform: translateY(-2px);
}

.advantage-grid {
	display: grid;
	grid-template-columns: repeat(3, minmax(0, 1fr));
	gap: 18px;
}

.advantage-card {
	display: flex;
	gap: 14px;
	padding: 24px;
}

.advantage-card .v-icon {
	color: #7CFFB2;
}

.contact-section {
	padding-bottom: 140px;
}

.contact-panel {
	display: grid;
	grid-template-columns: minmax(0, 1fr) auto;
	gap: 28px;
	align-items: center;
	padding: clamp(28px, 5vw, 54px);
}

.contact-actions {
	display: flex;
	flex-wrap: wrap;
	justify-content: flex-end;
	gap: 10px;
}

.contact-copy-tip {
	display: inline-flex;
	grid-column: 1 / -1;
	width: fit-content;
	padding: 6px 10px;
	border: 1px solid rgba(124, 255, 178, 0.28);
	border-radius: 8px;
	background: rgba(124, 255, 178, 0.08);
	color: #7CFFB2;
	font-size: 12px;
	font-weight: 700;
}

.project-dialog__panel {
	position: relative;
	display: grid;
	grid-template-columns: minmax(280px, 0.9fr) minmax(0, 1.1fr);
	gap: 0;
	border: 1px solid rgba(221, 247, 255, 0.16);
	border-radius: 24px;
	background:
		linear-gradient(145deg, rgba(255, 255, 255, 0.12), rgba(0, 229, 255, 0.04)),
		rgba(7, 13, 26, 0.86);
	backdrop-filter: blur(22px);
	box-shadow: 0 30px 100px rgba(0, 0, 0, 0.46);
	overflow: hidden;
	color: #fff;
}

.project-dialog__close {
	position: absolute;
	top: 14px;
	right: 14px;
	z-index: 3;
	width: 38px;
	height: 38px;
	border: 1px solid rgba(221, 247, 255, 0.16);
	border-radius: 999px;
	background: rgba(6, 12, 24, 0.5);
	color: #fff;
	cursor: pointer;
	backdrop-filter: blur(14px);
}

.project-dialog__media {
	position: relative;
	min-height: 520px;
	overflow: hidden;
}

.project-dialog__media img {
	width: 100%;
	height: 100%;
	object-fit: cover;
	display: block;
	filter: saturate(1.05) contrast(1.04);
}

.project-dialog__media::after {
	content: "";
	position: absolute;
	inset: 0;
	background: linear-gradient(90deg, transparent, rgba(7, 13, 26, 0.76));
	pointer-events: none;
}

.project-dialog__media span {
	position: absolute;
	left: 18px;
	top: 18px;
	z-index: 2;
	padding: 7px 11px;
	border: 1px solid;
	border-radius: 999px;
	background: rgba(6, 12, 24, 0.62);
	backdrop-filter: blur(12px);
	font-size: 12px;
	font-weight: 950;
}

.project-dialog__copy {
	position: relative;
	z-index: 1;
	display: flex;
	flex-direction: column;
	justify-content: center;
	padding: clamp(28px, 5vw, 54px);
}

.project-dialog__copy > small {
	color: #7CFFB2;
	font-size: 12px;
	font-weight: 950;
	text-transform: uppercase;
}

.project-dialog__copy h3 {
	margin: 10px 0 0;
	font-size: 44px;
	line-height: 1;
	font-weight: 950;
	letter-spacing: 0;
}

.project-dialog__copy p {
	margin: 18px 0 0;
	color: rgba(255, 255, 255, 0.72);
	line-height: 1.75;
}

.project-dialog__stack,
.project-dialog__actions {
	display: flex;
	flex-wrap: wrap;
	gap: 10px;
	margin-top: 22px;
}

.project-dialog__stack span {
	padding: 7px 10px;
	border: 1px solid rgba(221, 247, 255, 0.16);
	border-radius: 999px;
	background: rgba(255, 255, 255, 0.06);
	color: rgba(221, 247, 255, 0.86);
	font-size: 12px;
	font-weight: 850;
}

.project-dialog__points {
	display: grid;
	gap: 12px;
	margin-top: 24px;
}

.project-dialog__points div {
	display: flex;
	gap: 12px;
	padding: 14px;
	border: 1px solid rgba(221, 247, 255, 0.12);
	border-radius: 16px;
	background: rgba(255, 255, 255, 0.04);
}

.project-dialog__points .v-icon {
	color: #7CFFB2;
	flex: 0 0 auto;
}

.project-dialog__points span {
	display: grid;
	gap: 3px;
}

.project-dialog__points strong {
	font-size: 13px;
}

.project-dialog__points small {
	color: rgba(255, 255, 255, 0.62);
	font-size: 12px;
	line-height: 1.5;
}

.project-dialog__actions a,
.project-dialog__actions button {
	padding: 10px 13px;
	border: 1px solid rgba(221, 247, 255, 0.16);
	border-radius: 999px;
	background: rgba(255, 255, 255, 0.06);
	color: rgba(255, 255, 255, 0.88);
	cursor: pointer;
	font: inherit;
	font-size: 13px;
	font-weight: 900;
	text-decoration: none;
	transition: background 0.25s ease, transform 0.25s ease;
}

.project-dialog__actions a:hover,
.project-dialog__actions button:hover {
	background: rgba(124, 255, 178, 0.12);
	transform: translateY(-2px);
}

.command-overlay {
	position: fixed;
	inset: 0;
	z-index: 180;
	display: flex;
	align-items: flex-start;
	justify-content: center;
	padding: 10vh 18px 18px;
	background: rgba(2, 6, 14, 0.56);
	backdrop-filter: blur(10px);
}

.command-palette {
	width: min(720px, 100%);
	border: 1px solid rgba(221, 247, 255, 0.16);
	border-radius: 22px;
	background:
		linear-gradient(145deg, rgba(255, 255, 255, 0.12), rgba(0, 229, 255, 0.035)),
		rgba(7, 13, 26, 0.9);
	box-shadow: 0 30px 100px rgba(0, 0, 0, 0.48);
	overflow: hidden;
	color: #fff;
}

.command-palette__search {
	display: grid;
	grid-template-columns: auto minmax(0, 1fr) auto;
	align-items: center;
	gap: 12px;
	padding: 16px;
	border-bottom: 1px solid rgba(221, 247, 255, 0.12);
}

.command-palette__search .v-icon {
	color: #7CFFB2;
}

.command-palette__search input {
	width: 100%;
	min-width: 0;
	border: 0;
	outline: 0;
	background: transparent;
	color: #fff;
	font-size: 17px;
	font-weight: 850;
}

.command-palette__search input::placeholder {
	color: rgba(221, 247, 255, 0.46);
}

.command-palette__search button {
	width: 34px;
	height: 34px;
	border: 1px solid rgba(221, 247, 255, 0.14);
	border-radius: 999px;
	background: rgba(255, 255, 255, 0.06);
	color: #fff;
	cursor: pointer;
}

.command-palette__list {
	max-height: min(520px, 58vh);
	overflow-y: auto;
	padding: 10px;
}

.command-palette__list button {
	display: grid;
	grid-template-columns: auto minmax(0, 1fr) auto;
	align-items: center;
	gap: 12px;
	width: 100%;
	padding: 13px;
	border: 1px solid transparent;
	border-radius: 16px;
	background: transparent;
	color: #fff;
	cursor: pointer;
	text-align: left;
	transition: border-color 0.2s ease, background 0.2s ease, transform 0.2s ease;
}

.command-palette__list button:hover {
	border-color: rgba(124, 255, 178, 0.28);
	background: rgba(124, 255, 178, 0.1);
	transform: translateY(-1px);
}

.command-palette__list button > .v-icon {
	color: #7CFFB2;
}

.command-palette__list span {
	min-width: 0;
	display: grid;
	gap: 3px;
}

.command-palette__list strong {
	overflow: hidden;
	text-overflow: ellipsis;
	white-space: nowrap;
	font-size: 14px;
	font-weight: 950;
}

.command-palette__list small {
	overflow: hidden;
	text-overflow: ellipsis;
	white-space: nowrap;
	color: rgba(255, 255, 255, 0.58);
	font-size: 12px;
	font-weight: 700;
}

.command-palette__list em {
	padding: 5px 8px;
	border-radius: 999px;
	background: rgba(255, 255, 255, 0.06);
	color: rgba(221, 247, 255, 0.74);
	font-size: 11px;
	font-style: normal;
	font-weight: 900;
}

.command-palette__empty {
	padding: 30px 16px;
	color: rgba(255, 255, 255, 0.58);
	text-align: center;
	font-size: 13px;
	font-weight: 800;
}

@keyframes scroll-pulse {
	0%, 100% {
		transform: translateY(0);
		opacity: 0.74;
	}
	50% {
		transform: translateY(6px);
		opacity: 1;
	}
}

@keyframes profile-particle-drift {
	0%, 100% {
		opacity: 0.16;
		transform: translate3d(0, 0, 0) scale(0.82);
	}
	45% {
		opacity: 0.72;
		transform: translate3d(18px, -28px, 0) scale(1.15);
	}
	72% {
		opacity: 0.36;
		transform: translate3d(-12px, -44px, 0) scale(0.95);
	}
}

@keyframes profile-orbit {
	from {
		transform: rotate(0deg);
	}
	to {
		transform: rotate(360deg);
	}
}

@keyframes project-marquee {
	from {
		transform: translateX(0);
	}
	to {
		transform: translateX(-50%);
	}
}

@media (prefers-reduced-motion: reduce) {
	.project-flow__track,
	.hero-stage__scroll,
	.profile-particle,
	.profile-card--chart::after {
		animation: none;
	}
}

@media (min-width: 1440px) {
	.hero-stage__title {
		font-size: 108px;
	}

	.hero-stage__title span:first-child {
		font-size: 78px;
	}
}

@media (max-width: 1200px) {
	.hero-stage__title {
		font-size: 72px;
	}

	.hero-stage__title span:first-child {
		font-size: 56px;
	}

	.hero-stage__intro {
		font-size: 17px;
	}
}

@media (max-width: 960px) {
	.hero-stage {
		padding: 18px;
		min-height: 100svh;
	}

	.hero-stage__topline {
		align-items: flex-start;
		grid-template-columns: 1fr;
		gap: 10px;
	}

	.hero-search--top {
		order: 3;
		justify-self: stretch;
		max-width: none;
	}

	.hero-stage__nav {
		order: 2;
		justify-self: end;
	}

	.hero-stage__links {
		display: none;
		max-width: 220px;
		justify-content: flex-end;
		flex-wrap: wrap;
		border-radius: 18px;
	}

	.hero-menu-toggle {
		display: inline-flex;
		align-items: center;
		justify-content: center;
	}

	.hero-mobile-menu {
		display: grid;
		gap: 4px;
	}

	.hero-stage__brand {
		order: 1;
	}

	.hero-search {
		min-height: 38px;
	}

	.hero-search__engine {
		max-width: 92px;
	}

	.hero-search__engine span {
		overflow: hidden;
		text-overflow: ellipsis;
	}

	.hero-search input {
		font-size: 11px;
	}

	.hero-stage__links a,
	.hero-stage__links button {
		padding: 6px 8px;
		font-size: 12px;
	}

	.hero-stage__body {
		min-height: calc(100svh - 178px);
		padding: 22px 0 14px;
	}

	.hero-stage__title {
		font-size: 42px;
	}

	.hero-stage__title span:first-child {
		font-size: 36px;
	}

	.hero-stage__intro {
		margin-top: 14px;
		font-size: 15px;
		line-height: 1.55;
	}

	.hero-stage__meta {
		display: none;
	}

	.hero-stage__actions {
		margin-top: 18px;
		gap: 8px;
	}

	.hero-stage__action {
		min-height: 36px;
	}

	.section-heading h2,
	.contact-panel h2 {
		font-size: 36px;
	}

	.profile-card h3,
	.advantage-card h3,
	.project-showcase h3 {
		font-size: 24px;
	}

	.hero-stage__bottom {
		display: flex;
		flex-direction: column;
		align-items: center;
		min-height: 24px;
	}

	.hero-stage__quote {
		display: none;
	}

	.profile-card--intro,
	.contact-panel {
		grid-template-columns: 1fr;
	}

	.profile-grid,
	.advantage-grid {
		grid-template-columns: 1fr;
	}

	.profile-card--intro img {
		width: 112px;
		height: 112px;
		border-radius: 22px;
	}

	.profile-focus-tabs button {
		width: 100%;
		justify-content: flex-start;
	}

	.profile-card--focus {
		min-height: auto;
	}

	.project-detail {
		grid-template-columns: 1fr;
		min-height: 0;
	}

	.project-dialog__panel {
		grid-template-columns: 1fr;
		max-height: calc(100vh - 24px);
		overflow-y: auto;
	}

	.project-dialog__media {
		min-height: 220px;
	}

	.project-dialog__media::after {
		background: linear-gradient(180deg, transparent, rgba(7, 13, 26, 0.82));
	}

	.project-dialog__copy {
		padding: 24px;
	}

	.project-dialog__copy h3 {
		font-size: 30px;
	}

	.command-overlay {
		padding-top: 7vh;
	}

	.command-palette__search input {
		font-size: 14px;
	}

	.project-detail__preview {
		order: -1;
		min-height: 210px;
	}

	.project-detail__preview::after {
		background: linear-gradient(180deg, transparent, rgba(5, 10, 22, 0.78));
	}

	.project-detail__copy h3 {
		font-size: 30px;
	}

	.project-flow {
		margin: 0 -18px;
		padding: 6px 18px 14px;
		mask-image: linear-gradient(90deg, transparent, #000 6%, #000 94%, transparent);
	}

	.project-showcase {
		flex-basis: 304px;
		width: 304px;
	}

	.project-showcase__image {
		height: 178px;
	}

	.project-showcase__body {
		min-height: 232px;
	}

	.contact-actions {
		justify-content: flex-start;
	}
}
</style>
