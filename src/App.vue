<template>
  <main class="app" :data-night="isNight">
    <!-- 헤더 -->
    <header class="hero">
      <button class="orb" @click="toggleTheme" :title="isNight ? '아침으로 전환' : '밤으로 전환'" aria-label="테마 전환">
        <svg v-if="isNight" viewBox="0 0 120 120" class="moon">
          <defs>
            <radialGradient id="g-moon" cx="50%" cy="50%" r="60%">
              <stop offset="0%" stop-color="#fff9d6" />
              <stop offset="60%" stop-color="#ffeaa7" />
              <stop offset="100%" stop-color="#f7c76a" />
            </radialGradient>
          </defs>
          <circle cx="60" cy="60" r="46" fill="url(#g-moon)" />
          <circle cx="45" cy="48" r="6" fill="#eacb7d" opacity="0.5"/>
          <circle cx="73" cy="70" r="4" fill="#e8c270" opacity="0.45"/>
        </svg>
        <svg v-else viewBox="0 0 120 120" class="sun">
          <defs>
            <radialGradient id="g-sun" cx="50%" cy="50%" r="60%">
              <stop offset="0%" stop-color="#fff4a3"/>
              <stop offset="60%" stop-color="#ffd166"/>
              <stop offset="100%" stop-color="#ff9f1c"/>
            </radialGradient>
          </defs>
          <circle cx="60" cy="60" r="44" fill="url(#g-sun)"/>
          <g stroke="#ffad33" stroke-width="5" stroke-linecap="round">
            <line v-for="i in 12" :key="i" x1="60" y1="8" x2="60" y2="0" :transform="`rotate(${i*30} 60 60)`"/>
          </g>
        </svg>
      </button>

      <div class="title">
        <span class="badge">中秋</span>
        <h1>풍성한 한가위 되세요</h1>
        <p class="subtitle">보름달처럼 마음도 꽉 찬 명절 되길 바라요 🌕</p>
      </div>
    </header>

    <!-- 덕담 카드 -->
    <section class="card">
      <p class="wish">{{ currentWish }}</p>
      <div class="actions">
        <button class="btn" @click="shuffleWish">다른 덕담 보기</button>
      </div>
      <div class="obang">
        <span class="c-blue" title="청(東)"></span>
        <span class="c-red" title="적(南)"></span>
        <span class="c-yellow" title="황(中)"></span>
        <span class="c-white" title="백(西)"></span>
        <span class="c-black" title="흑(北)"></span>
      </div>

      <label class="note">
        <textarea v-model="note" placeholder="전하고 싶은 한마디를 적어보세요…"></textarea>
      </label>
      <button class="btn ghost" @click="clearNote" :disabled="note.trim().length === 0">메모 지우기</button>
    </section>

    <!-- 연등 몇 개만 부드럽게 -->
    <ul class="lanterns" aria-hidden="true">
      <li v-for="i in 6" :key="i" :style="lanternStyle(i)"></li>
    </ul>

    <!-- 한지 텍스처 -->
    <div class="hanji" aria-hidden="true"></div>
  </main>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue';

const isNight = ref(true);
const note = ref('');

/** ✅ 최소 1개 이상 원소가 있는 튜플 타입로 선언 */
const wishesNonEmpty: [string, ...string[]] = [
  '보름달처럼 넉넉한 행복이 가득하시길!',
  '달에게 빈 소원, 올가을에 이루어지길 바랍니다.',
  '멀리 있어도 마음은 한가위처럼 한곳에 😊',
  '가족과 웃음꽃 피는 풍성한 연휴 되세요.',
  '건강하고 달달한 추석 보내세요! 송편처럼요 🥟',
];

/** 필요하면 일반 배열처럼도 쓰려고 별도 참조 */
const wishes: readonly string[] = wishesNonEmpty;

/** ✅ ref<string> + 초기값은 튜플의 [0] (undefined 불가) */
const currentWish = ref<string>(wishesNonEmpty[0]);

/** ✅ 파라미터도 “비어있지 않은 배열”을 받도록 타입 보장 */
function pickRandom(arr: readonly [string, ...string[]]): string {
  const idx = Math.floor(Math.random() * arr.length);
  return arr[idx]; // 여기선 절대 undefined 아님
}

function shuffleWish() {
  currentWish.value = pickRandom(wishesNonEmpty);
}

function toggleTheme() {
  isNight.value = !isNight.value;
}

function clearNote() {
  note.value = '';
}

onMounted(() => {
  setInterval(shuffleWish, 6000);
});

/** 연등 스타일 */
function lanternStyle(i: number) {
  const left = (i * 15 + 10) % 100;
  const delay = (i % 5) * 0.8;
  const dur = 9 + (i % 4) * 2;
  const scale = 0.8 + (i % 3) * 0.12;
  return {
    left: `${left}%`,
    animationDelay: `${delay}s`,
    animationDuration: `${dur}s`,
    transform: `scale(${scale})`,
  } as const;
}
</script>


<style scoped>
/* 배경/레이아웃 */
.app {
  --bg-night: linear-gradient(180deg, #0b132b 0%, #1c2541 60%, #3a506b 100%);
  --bg-day: linear-gradient(180deg, #9be2ff 0%, #c7f5ff 60%, #fef6e4 100%);
  --ink: #1b1b1b;
  --ink-soft: #4a4a4a;
  --card: rgba(255,255,255,0.92);
  --card-border: rgba(0,0,0,0.08);

  min-height: 100svh;
  display: grid;
  grid-template-rows: auto 1fr;
  place-items: center;
  overflow: hidden;
  position: relative;
  background: var(--bg-night);
  color: var(--ink);
  transition: background 700ms ease;
  padding: clamp(16px, 3vw, 32px);
}
.app[data-night="false"] { background: var(--bg-day); }

/* 한지 텍스처 */
.hanji {
  position: absolute; inset: 0;
  background:
      radial-gradient(rgba(0,0,0,0.06) 1px, transparent 1px) 0 0/22px 22px,
      radial-gradient(rgba(0,0,0,0.035) 1px, transparent 1px) 11px 11px/22px 22px;
  opacity: 0.35; mix-blend-mode: multiply; pointer-events: none;
}

/* Hero */
.hero {
  width: 100%; max-width: 980px;
  display: grid;
  grid-template-columns: 1fr auto;
  align-items: start;
  gap: 16px;
}
.title {
  text-align: right;
}
.title h1 {
  font-size: clamp(26px, 5vw, 42px);
  margin: 6px 0 4px;
  letter-spacing: 0.02em;
  font-weight: 800;
  background: linear-gradient(90deg, #8b5cf6, #f59e0b, #ef4444);
  -webkit-background-clip: text; background-clip: text; color: transparent;
}
.subtitle { margin: 0; color: var(--ink-soft); }
.badge {
  display: inline-block;
  font-size: 12px; padding: 4px 8px; border-radius: 999px;
  background: rgba(245,183,0,0.15); color: #a36b00; border: 1px solid rgba(245,183,0,0.35);
  letter-spacing: 0.2em;
}

/* 달/해 버튼 */
.orb {
  justify-self: start;
  width: clamp(72px, 12vw, 120px); aspect-ratio: 1;
  border: none; background: transparent; padding: 0; cursor: pointer;
  filter: drop-shadow(0 6px 20px rgba(255, 232, 180, 0.35));
  transition: transform 200ms ease;
}
.orb:hover { transform: scale(1.03) rotate(-2deg); }
.moon, .sun { width: 100%; height: 100%; display: block; }

/* 카드 */
.card {
  position: relative;
  width: min(960px, 92vw);
  background: var(--card);
  backdrop-filter: blur(8px);
  border: 1px solid var(--card-border);
  box-shadow: 0 18px 55px rgba(0,0,0,0.18);
  border-radius: 20px;
  padding: clamp(16px, 3.5vw, 28px);
  margin-top: clamp(14px, 3vw, 24px);
  z-index: 2;
}
.wish {
  text-align: center;
  font-size: clamp(16px, 2.4vw, 20px);
  margin: 2px 0 14px;
}
.actions { display: flex; justify-content: center; }

/* 버튼 */
.btn {
  appearance: none; border: none;
  background: linear-gradient(90deg, #f59e0b, #ef4444);
  color: white; padding: 10px 14px; border-radius: 10px; font-weight: 700;
  cursor: pointer; transition: transform 120ms ease, filter 120ms ease;
}
.btn:hover { transform: translateY(-1px); filter: brightness(1.05); }
.btn:active { transform: translateY(0); }
.btn.ghost { background: transparent; border: 1px solid rgba(0,0,0,0.15); color: var(--ink); margin-top: 10px; }

/* 오방색 */
.obang {
  display: grid; grid-template-columns: repeat(5, 1fr);
  gap: 6px; margin: 12px auto; max-width: 280px;
}
.obang span { display:block; height: 16px; border-radius: 999px; box-shadow: inset 0 0 0 1px rgba(0,0,0,0.12); }
.c-blue { background:#0ea5e9; } .c-red{background:#ef4444;} .c-yellow{background:#fbbf24;}
.c-white{background:#e5e7eb;} .c-black{background:#111827;}

/* 메모 */
.note textarea {
  width: 100%; min-height: 90px; resize: vertical;
  padding: 10px 12px; border-radius: 10px; border: 1px solid rgba(0,0,0,0.12);
  background: #fffdfa;
  font-family: ui-sans-serif, system-ui, -apple-system, "Segoe UI", Roboto, "Noto Sans KR", "Apple SD Gothic Neo", sans-serif;
}

/* 연등 */
.lanterns { list-style:none; margin:0; padding:0; position:absolute; inset-inline:0; bottom:-20vh; }
.lanterns li {
  position:absolute; bottom:-20vh; width:26px; height:34px;
  background: linear-gradient(#ffecd1, #ffc48a);
  border:2px solid rgba(0,0,0,0.1);
  border-radius:6px 6px 10px 10px;
  box-shadow: 0 8px 16px rgba(0,0,0,0.15), 0 0 20px rgba(255,160,64,0.45);
  animation: floatUp linear infinite;
}
.lanterns li::before, .lanterns li::after { content:''; position:absolute; left:50%; transform:translateX(-50%); }
.lanterns li::before { top:-10px; width:2px; height:14px; background:rgba(255,255,255,0.5); }
.lanterns li::after { bottom:-10px; width:6px; height:6px; background:#ffbe76; border-radius:50%; box-shadow:0 0 10px rgba(255,190,118,0.8); }
@keyframes floatUp { from{transform:translateY(0)} to{transform:translateY(-120vh)} }

.app[data-night="false"] .lanterns li { opacity:.75; box-shadow: 0 6px 12px rgba(0,0,0,0.1), 0 0 0 rgba(255,160,64,0); }
</style>
