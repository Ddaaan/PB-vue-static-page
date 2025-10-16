<template>
  <main class="app" :data-night="isNight">
    <div class="sky">
      <div class="stars">
        <i v-for="n in 80" :key="n" class="star" />
      </div>

      <button class="orb" @click="toggleTheme" aria-label="Toggle day/night" :title="isNight ? '아침으로 전환' : '밤으로 전환'">
        <svg v-if="isNight" viewBox="0 0 120 120" class="moon">
          <defs>
            <radialGradient id="g-moon" cx="50%" cy="50%" r="60%">
              <stop offset="0%" stop-color="#fff9d6" />
              <stop offset="60%" stop-color="#ffeaa7" />
              <stop offset="100%" stop-color="#f7c76a" />
            </radialGradient>
          </defs>
          <circle cx="60" cy="60" r="45" fill="url(#g-moon)"/>
          <circle cx="45" cy="48" r="6" fill="#eacb7d" opacity="0.5"/>
          <circle cx="73" cy="70" r="4" fill="#e8c270" opacity="0.45"/>
          <g fill="#f9f3d6" opacity="0.9">
            <ellipse cx="60" cy="78" rx="13" ry="8"/>
            <ellipse cx="53" cy="64" rx="5.2" ry="7.8" transform="rotate(-18 53 64)"/>
            <ellipse cx="67" cy="62" rx="5.2" ry="7.8" transform="rotate(18 67 62)"/>
            <circle cx="60" cy="67" r="4"/>
          </g>
        </svg>
        <svg v-else viewBox="0 0 120 120" class="sun">
          <defs>
            <radialGradient id="g-sun" cx="50%" cy="50%" r="60%">
              <stop offset="0%" stop-color="#fff4a3"/>
              <stop offset="60%" stop-color="#ffd166"/>
              <stop offset="100%" stop-color="#ff9f1c"/>
            </radialGradient>
          </defs>
          <circle cx="60" cy="60" r="42" fill="url(#g-sun)"/>
          <g stroke="#ffad33" stroke-width="5" stroke-linecap="round">
            <!-- r 미사용 제거 -->
            <line v-for="i in 12" :key="i" x1="60" y1="8" x2="60" y2="0" :transform="`rotate(${i*30} 60 60)`"/>
          </g>
        </svg>
      </button>

      <!-- 카운트 기반 반복으로 변경 -->
      <ul class="lanterns" aria-hidden="true">
        <li v-for="i in LANTERN_COUNT" :key="i" :style="lanternStyle(i - 1)"></li>
      </ul>
    </div>

    <section class="card">
      <header class="title">
        <span class="badge">中秋</span>
        <h1>풍성한 한가위 되세요</h1>
        <p class="subtitle">보름달처럼 마음도 꽉 찬 명절 되길 바라요 🌕</p>
      </header>

      <div class="wish">
        <p>{{ currentWish }}</p>
        <button class="btn" @click="shuffleWish">다른 덕담 보기</button>
      </div>

      <div class="grid">
        <article class="panel">
          <h3>한가위 키워드</h3>
          <ul>
            <li>보름달 소원 빌기</li>
            <li>송편 빚기 & 나눔</li>
            <li>가족 안부 & 감사</li>
          </ul>
        </article>
        <article class="panel">
          <h3>오방색 포인트</h3>
          <div class="obang">
            <span class="c-blue" title="청(東)"></span>
            <span class="c-red" title="적(南)"></span>
            <span class="c-yellow" title="황(中)"></span>
            <span class="c-white" title="백(西)"></span>
            <span class="c-black" title="흑(北)"></span>
          </div>
          <p class="hint">전통색을 살짝만 써도 분위기 UP ✨</p>
        </article>
        <article class="panel">
          <h3>메모</h3>
          <label class="note">
            <textarea v-model="note" placeholder="전하고 싶은 한마디를 적어보세요…"></textarea>
          </label>
          <button class="btn ghost" @click="clearNote" :disabled="!note.trim()">메모 지우기</button>
        </article>
      </div>

      <footer class="foot">
        <small>Made with ❤️ for Chuseok — click the {{ isNight ? 'moon' : 'sun' }} to toggle theme</small>
      </footer>
    </section>

    <div class="hanji" aria-hidden="true"></div>
  </main>
</template>

<script setup lang="ts">
import { ref, onMounted /*, onUnmounted*/ } from 'vue';

const isNight = ref(true);
const note = ref('');
const LANTERN_COUNT = 8 as const;

// ✅ string[] 로 넓혀서 선언
const wishes: string[] = [
  '보름달처럼 넉넉한 행복이 가득하시길!',
  '달에게 빈 소원, 올가을에 이루어지길 바랍니다.',
  '멀리 있어도 마음은 한가위처럼 한곳에 😊',
  '가족과 웃음꽃 피는 풍성한 연휴 되세요.',
  '건강하고 달달한 추석 보내세요! 송편처럼요 🥟',
];

// ✅ ref<string> 으로 타입 고정
const currentWish = ref<string>(wishes[0]);

function shuffleWish() {
  const idx = Math.floor(Math.random() * wishes.length);
  // ✅ 혹시 모를 범위 밖 인덱스에 대비 (TS 만족)
  currentWish.value = wishes[idx] ?? wishes[0];
}

function toggleTheme() {
  isNight.value = !isNight.value;
}

function clearNote() {
  note.value = '';
}

// unused 변수 없이 인터벌 실행
onMounted(() => {
  setInterval(shuffleWish, 6000);
  // 필요하면 해제 로직 추가:
  // const timer = window.setInterval(shuffleWish, 6000);
  // onUnmounted(() => clearInterval(timer));
});

function lanternStyle(i: number) {
  const left = (i * 12 + 5) % 100;
  const delay = (i % 5) * 1.2;
  const dur = 8 + (i % 4) * 2;
  const scale = 0.7 + (i % 3) * 0.15;
  return {
    left: `${left}%`,
    animationDelay: `${delay}s`,
    animationDuration: `${dur}s`,
    transform: `scale(${scale})`,
  } as const;
}
</script>

<style scoped>
/* (스타일은 기존과 동일) */
/* ... 그대로 두셔도 됩니다 ... */
/* Root & layout */
.app {
  --bg-night: linear-gradient(180deg, #0b132b 0%, #1c2541 60%, #3a506b 100%);
  --bg-day: linear-gradient(180deg, #9be2ff 0%, #c7f5ff 60%, #fef6e4 100%);
  --ink: #1b1b1b;
  --ink-soft: #4a4a4a;
  --card: rgba(255,255,255,0.9);
  --card-border: rgba(0,0,0,0.08);
  --accent: #f5b700;
  --accent-2: #6c63ff;

  min-height: 100svh;
  display: grid;
  place-items: center;
  overflow: hidden;
  position: relative;
  background: var(--bg-night);
  color: var(--ink);
  transition: background 700ms ease;
  padding: clamp(16px, 3vw, 32px);
}
.app[data-night="false"] { background: var(--bg-day); }

/* Hanji paper border */
.hanji { position: absolute; inset: 0;
  background:
      radial-gradient(rgba(0,0,0,0.06) 1px, transparent 1px) 0 0/22px 22px,
      radial-gradient(rgba(0,0,0,0.035) 1px, transparent 1px) 11px 11px/22px 22px;
  opacity: 0.35; mix-blend-mode: multiply; pointer-events: none; }

/* Sky and stars */
.sky { position: absolute; inset: 0; overflow: hidden; }
.stars { position: absolute; inset: 0; }
.star {
  position: absolute; width: 2px; height: 2px; background: #fff8d6;
  top: calc(var(--i) * 7 % 100 * 1%); left: calc(var(--i) * 13 % 100 * 1%);
  border-radius: 50%; opacity: 0.9; animation: twinkle 2.8s infinite ease-in-out;
}
.star:nth-child(odd) { width: 1px; height: 1px; opacity: 0.7; }
.star:nth-child(3n) { animation-duration: 3.6s; }
@keyframes twinkle { 0%,100%{opacity:.4;transform:scale(1)}50%{opacity:1;transform:scale(1.6)} }

/* Moon/Sun button */
.orb {
  position: absolute; right: clamp(16px, 4vw, 40px); top: clamp(16px, 4vw, 40px);
  width: clamp(72px, 12vw, 120px); aspect-ratio: 1; border: none; background: transparent; padding: 0; cursor: pointer;
  filter: drop-shadow(0 6px 20px rgba(255, 232, 180, 0.35)); transition: transform 200ms ease;
}
.orb:hover { transform: scale(1.03) rotate(-2deg); }
.moon, .sun { width: 100%; height: 100%; display: block; }

/* Lanterns */
.lanterns { list-style: none; margin: 0; padding: 0; position: absolute; inset-inline: 0; bottom: -20vh; }
.lanterns li {
  position: absolute; bottom: -20vh; width: 28px; height: 36px;
  background: linear-gradient(#ffecd1, #ffc48a); border: 2px solid rgba(0,0,0,0.1);
  border-radius: 6px 6px 10px 10px; box-shadow: 0 8px 16px rgba(0,0,0,0.15), 0 0 24px rgba(255,160,64,0.45);
  animation: floatUp linear infinite;
}
.lanterns li::before, .lanterns li::after { content: ''; position: absolute; left: 50%; transform: translateX(-50%); }
.lanterns li::before { top: -10px; width: 2px; height: 14px; background: rgba(255,255,255,0.5); }
.lanterns li::after { bottom: -10px; width: 6px; height: 6px; background: #ffbe76; border-radius: 50%; box-shadow: 0 0 10px rgba(255,190,118,0.8); }
@keyframes floatUp { from{transform:translateY(0)} to{transform:translateY(-120vh)} }

/* Content Card */
.card { position: relative; width: min(960px, 92vw); background: var(--card); backdrop-filter: blur(8px);
  border: 1px solid var(--card-border); box-shadow: 0 18px 55px rgba(0,0,0,0.18); border-radius: 20px;
  padding: clamp(16px, 3.5vw, 32px); z-index: 2; }
.title { text-align: center; margin-bottom: 18px; }
.title h1 { font-size: clamp(28px, 5vw, 42px); margin: 8px 0 6px; letter-spacing: .02em; font-weight: 800;
  background: linear-gradient(90deg, #8b5cf6, #f59e0b, #ef4444); -webkit-background-clip: text; background-clip: text; color: transparent; }
.subtitle { color: var(--ink-soft); margin: 0; }
.badge { display:inline-block; font-size:12px; padding:4px 8px; border-radius:999px; background: rgba(245,183,0,.15);
  color:#a36b00; border:1px solid rgba(245,183,0,.35); letter-spacing:.2em; }

/* Wish area */
.wish { display:grid; grid-template-columns:1fr auto; align-items:center; gap:12px; padding:16px; margin:14px 0 22px;
  border:1px dashed rgba(0,0,0,.12); border-radius:14px; background: linear-gradient(180deg, rgba(255,247,224,.7), rgba(255,255,255,.7));
  font-size: clamp(15px, 2.4vw, 18px); }

/* Buttons */
.btn { appearance:none; border:none; background: linear-gradient(90deg, #f59e0b, #ef4444); color:white; padding:10px 14px;
  border-radius:10px; font-weight:700; cursor:pointer; transition: transform 120ms ease, filter 120ms ease; }
.btn:hover { transform: translateY(-1px); filter: brightness(1.05); }
.btn:active { transform: translateY(0); }
.btn.ghost { background:transparent; border:1px solid rgba(0,0,0,.15); color:var(--ink); }

/* Grid panels */
.grid { display:grid; grid-template-columns:1fr; gap:14px; }
@media (min-width: 760px) { .grid { grid-template-columns: 1.1fr 1fr 1.2fr; } }
.panel { background:#ffffff; border:1px solid rgba(0,0,0,.06); border-radius:14px; padding:14px; min-height:120px; box-shadow:0 8px 24px rgba(0,0,0,.06); }
.panel h3 { margin:0 0 8px; font-size:16px; letter-spacing:.02em; }

/* Obang colors */
.obang { display:grid; grid-template-columns:repeat(5,1fr); gap:6px; margin:8px 0 10px; }
.obang span { display:block; height:16px; border-radius:999px; box-shadow: inset 0 0 0 1px rgba(0,0,0,.12); }
.c-blue { background:#0ea5e9; } .c-red{background:#ef4444;} .c-yellow{background:#fbbf24;} .c-white{background:#e5e7eb;} .c-black{background:#111827;}
.hint { color: var(--ink-soft); margin: 0; font-size: 13px; }

/* Note */
.note textarea { width:100%; min-height:88px; resize:vertical; padding:10px 12px; border-radius:10px; border:1px solid rgba(0,0,0,.12);
  background:#fffdfa; font-family: ui-sans-serif, system-ui, -apple-system, "Segoe UI", Roboto, "Noto Sans KR", "Apple SD Gothic Neo", sans-serif; }

/* Footer */
.foot { margin-top:10px; text-align:center; color: var(--ink-soft); }

/* Day vs Night tints */
.app[data-night="true"] .panel { background: rgba(255,255,255,0.96); }
.app[data-night="true"] .btn { box-shadow: 0 6px 18px rgba(255,140,0,0.25); }
.app[data-night="false"] .lanterns li { opacity: .75; box-shadow: 0 6px 12px rgba(0,0,0,0.1), 0 0 0 rgba(255,160,64,0); }
.app[data-night="false"] .star { display: none; }
</style>
