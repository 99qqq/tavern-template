<template>
  <!-- 外层容器，居中显示手机壳 -->
  <div class="w-full flex justify-center py-4 box-border">
    <!-- 手机壳 -->
    <div
      class="relative w-full max-w-[360px] aspect-[9/19.5] rounded-[2.5rem] border border-slate-700 bg-slate-900 text-slate-50 shadow-xl overflow-hidden flex flex-col"
    >
      <!-- 顶部刘海与系统状态栏 -->
      <div class="pt-4 pb-1 flex flex-col items-center">
        <!-- 刘海 -->
        <div
          class="h-6 bg-black/80 rounded-[999px] px-5 flex items-center justify-between text-[10px] text-slate-100 min-w-[180px]"
        >
          <span class="tracking-wide">{{ timeText }}</span>
          <div class="flex items-center gap-1.5">
            <span class="inline-block w-3 h-3 rounded-full bg-emerald-400" />
            <div class="flex items-end gap-[1px]">
              <span class="w-[2px] h-1.5 bg-slate-200 rounded-[1px]" />
              <span class="w-[2px] h-2 bg-slate-200/80 rounded-[1px]" />
              <span class="w-[2px] h-2.5 bg-slate-200/70 rounded-[1px]" />
              <span class="w-[3px] h-3 bg-slate-50 rounded-[1px]" />
            </div>
          </div>
        </div>
      </div>

      <!-- 主体内容 -->
      <div class="flex-1 flex flex-col gap-3 px-4 pb-4">
        <!-- 顶部角色信息 -->
        <header class="flex items-center gap-3 mt-1">
          <div
            class="w-10 h-10 rounded-2xl bg-gradient-to-tr from-emerald-400 via-sky-400 to-indigo-500 flex items-center justify-center text-lg font-semibold shadow-md"
          >
            {{ avatarInitial }}
          </div>
          <div class="min-w-0">
            <div class="flex items-center gap-1.5">
              <p class="text-sm font-semibold truncate">
                {{ status.name }}
              </p>
              <span
                class="px-1.5 py-[1px] rounded-full bg-emerald-500/20 text-[10px] text-emerald-300 border border-emerald-400/40 shrink-0"
              >
                {{ status.mood }}
              </span>
            </div>
            <p class="text-[11px] text-slate-300 truncate">
              {{ status.title }}
            </p>
          </div>
          <div class="ml-auto flex flex-col items-end gap-0.5 text-[10px] text-slate-300">
            <span class="flex items-center gap-1">
              <span class="w-1.5 h-1.5 rounded-full" :class="onlineDotClass" />
              <span>{{ status.online ? '在线' : '离线' }}</span>
            </span>
            <span class="opacity-80">Lv. {{ status.level }}</span>
          </div>
        </header>

        <!-- 核心状态栏（生命 / 魔力 / 体力 等） -->
        <section class="grid grid-cols-3 gap-1.5 text-[10px]">
          <div
            v-for="bar in mainBars"
            :key="bar.key"
            class="rounded-xl bg-slate-800/80 px-2 py-1.5 flex flex-col gap-0.5 border border-slate-700/70"
          >
            <div class="flex items-center justify-between">
              <span class="text-[10px] text-slate-200">{{ bar.label }}</span>
              <span class="text-[10px] font-medium text-slate-100">
                {{ bar.current }}/{{ bar.max }}
              </span>
            </div>
            <div class="h-1.5 rounded-full bg-slate-900/60 overflow-hidden">
              <div
                class="h-full rounded-full transition-all duration-300"
                :class="bar.class"
                :style="{ width: bar.percent + '%' }"
              />
            </div>
          </div>
        </section>

        <!-- 辅助状态 & 位置 -->
        <section class="flex gap-1.5 text-[10px]">
          <div class="flex-1 rounded-xl bg-slate-800/80 px-2 py-1.5 border border-slate-700/70">
            <div class="flex items-center justify-between mb-0.5">
              <span class="text-slate-200">状态</span>
              <span class="text-[9px] text-slate-400">{{ status.stateDuration }}</span>
            </div>
            <div class="flex flex-wrap gap-1">
              <span
                v-for="tag in status.tags"
                :key="tag"
                class="px-1.5 py-[1px] rounded-full bg-slate-900/80 text-[9px] text-slate-200 border border-slate-600/70"
              >
                {{ tag }}
              </span>
            </div>
          </div>
          <div
            class="w-[38%] rounded-xl bg-slate-800/80 px-2 py-1.5 border border-slate-700/70 flex flex-col justify-between"
          >
            <span class="text-slate-200 mb-0.5">位置</span>
            <p class="text-[9px] text-slate-300 leading-snug line-clamp-2">
              {{ status.location }}
            </p>
          </div>
        </section>

        <!-- 聊天内容区域 -->
        <main class="flex-1 rounded-2xl bg-slate-950/40 border border-slate-800/80 px-2.5 py-2 flex flex-col gap-1.5 overflow-hidden">
          <div class="flex items-center justify-center gap-1 text-[10px] text-slate-400 mb-0.5">
            <span class="w-7 h-[1px] bg-slate-700/70" />
            <span>最近互动</span>
            <span class="w-7 h-[1px] bg-slate-700/70" />
          </div>

          <div class="flex-1 overflow-y-auto pr-0.5 space-y-1.5 text-[11px]">
            <!-- 系统提示 -->
            <p class="mx-auto max-w-[80%] text-center text-[10px] text-slate-400">
              {{ status.hint }}
            </p>

            <!-- 聊天气泡：角色 -->
            <div class="flex gap-1.5 items-end">
              <div
                class="w-6 h-6 rounded-xl bg-gradient-to-tr from-emerald-400/90 via-sky-400/90 to-indigo-500/90 flex items-center justify-center text-[11px] font-semibold shadow"
              >
                {{ avatarInitial }}
              </div>
              <div class="max-w-[70%] rounded-2xl rounded-bl-sm bg-slate-800/95 px-2 py-1.5">
                <p class="leading-snug text-slate-50 whitespace-pre-line">
                  {{ status.lastMessageFromCharacter }}
                </p>
              </div>
            </div>

            <!-- 聊天气泡：玩家 -->
            <div class="flex gap-1.5 items-end justify-end">
              <div class="max-w-[70%] rounded-2xl rounded-br-sm bg-emerald-500/90 px-2 py-1.5 text-right shadow">
                <p class="leading-snug text-slate-950 whitespace-pre-line">
                  {{ status.lastMessageFromPlayer }}
                </p>
              </div>
            </div>
          </div>
        </main>

        <!-- 底部输入栏 -->
        <footer class="mt-1 flex items-center gap-1.5 text-[11px]">
          <div class="flex-1 flex items-center gap-1.5 rounded-full bg-slate-800/80 px-2 py-1 border border-slate-700/80">
            <span class="w-1.5 h-1.5 rounded-full" :class="onlineDotClass" />
            <span class="truncate text-slate-300">
              {{ bottomHint }}
            </span>
          </div>
          <button
            type="button"
            class="shrink-0 w-8 h-8 rounded-full bg-emerald-500 text-slate-950 flex items-center justify-center text-xs font-semibold shadow-md active:scale-95 transition-transform"
          >
            ▶
          </button>
        </footer>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { computed, reactive } from 'vue';

interface CharacterStatus {
  name: string;
  title: string;
  mood: string;
  level: number;
  hp: number;
  hpMax: number;
  mp: number;
  mpMax: number;
  stamina: number;
  staminaMax: number;
  online: boolean;
  stateDuration: string;
  tags: string[];
  location: string;
  hint: string;
  lastMessageFromCharacter: string;
  lastMessageFromPlayer: string;
}

const status = reactive<CharacterStatus>({
  name: '缇娜·星辉',
  title: '「星光酒馆」驻店占星师',
  mood: '心情很好',
  level: 27,
  hp: 68,
  hpMax: 100,
  mp: 42,
  mpMax: 80,
  stamina: 55,
  staminaMax: 70,
  online: true,
  stateDuration: '最近 10 分钟',
  tags: ['专注占卜', '微醺', '灵感涌现'],
  location: '酒馆二楼·靠窗卡座\n可以看到夜空与街道灯火',
  hint: '以下信息仅为示例展示，用于在手机风格界面中显示角色当前状态。',
  lastMessageFromCharacter: '今晚的星象很温柔呢。\n如果你愿意，我可以为你抽一张只属于你的牌。',
  lastMessageFromPlayer: '等我把状态栏调好，我们再开始今晚的占卜吧。',
});

const timeText = computed(() => {
  const now = new Date();
  const h = now.getHours().toString().padStart(2, '0');
  const m = now.getMinutes().toString().padStart(2, '0');
  return `${h}:${m}`;
});

const avatarInitial = computed(() => status.name?.[0] ?? '角');

const mainBars = computed(() => {
  const clampPercent = (current: number, max: number) =>
    max <= 0 ? 0 : Math.max(0, Math.min(100, Math.round((current / max) * 100)));

  return [
    {
      key: 'hp',
      label: '生命',
      current: status.hp,
      max: status.hpMax,
      percent: clampPercent(status.hp, status.hpMax),
      class: 'bg-gradient-to-r from-rose-500 via-orange-400 to-amber-300',
    },
    {
      key: 'mp',
      label: '魔力',
      current: status.mp,
      max: status.mpMax,
      percent: clampPercent(status.mp, status.mpMax),
      class: 'bg-gradient-to-r from-sky-400 via-indigo-400 to-violet-400',
    },
    {
      key: 'stamina',
      label: '体力',
      current: status.stamina,
      max: status.staminaMax,
      percent: clampPercent(status.stamina, status.staminaMax),
      class: 'bg-gradient-to-r from-emerald-400 via-lime-400 to-amber-300',
    },
  ];
});

const onlineDotClass = computed(() =>
  status.online ? 'bg-emerald-400 shadow-[0_0_0_2px_rgba(16,185,129,0.4)]' : 'bg-slate-500',
);

const bottomHint = computed(
  () => `最近状态：${status.mood} · HP ${status.hp}/${status.hpMax} · MP ${status.mp}/${status.mpMax}`,
);
</script>

<style lang="scss" scoped>
/* 小屏下稍微压缩一下整体高度和圆角，使其在消息楼层内更紧凑 */
@media (max-width: 480px) {
  .w-full > div {
    border-radius: 1.75rem;
  }
}
</style>
