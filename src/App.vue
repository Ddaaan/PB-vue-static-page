<template>
  <main class="app" :data-night="isNight">
    <!-- 별이 있는 밤하늘 -->
    <div class="sky" aria-hidden="true">
      <i v-for="n in 80" :key="n" class="star" />
    </div>

    <!-- 떠다니는 풍등 -->
    <ul class="lanterns" aria-hidden="true">
      <li v-for="i in 8" :key="i" :style="lanternStyle(i - 1)"></li>
    </ul>

    <!-- 헤더: 보름달 & 밤 느낌 + 토글 -->
    <header class="hero">
      <div class="copy">
        <h1>
          <span class="eyebrow">BOREUMDAL NIGHT</span>
          보름달 아래, 잔잔한 밤의 안부
        </h1>
        <p class="subtitle">
          달빛처럼 포근한 한가위의 밤—가족의 웃음과 따뜻한 마음이 가득하길.
        </p>
      </div>

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
    </header>

    <!-- 본문 -->
    <section class="section">
      <h2>행복한 한가위!</h2>

      <div class="grid">
        <!-- 음식 -->
        <article class="panel">
          <h3>추석 음식</h3>
          <ul class="list">
            <li v-for="food in foods" :key="food.name">
              <span class="name">{{ food.name }}</span>
              <span class="dash">—</span>
              <span class="desc">{{ food.desc }}</span>
            </li>
          </ul>
        </article>

        <!-- 놀이 -->
        <article class="panel">
          <h3>추석 놀이</h3>
          <ul class="list">
            <li v-for="play in plays" :key="play.name">
              <span class="name">{{ play.name }}</span>
              <span class="dash">—</span>
              <span class="desc">{{ play.desc }}</span>
            </li>
          </ul>
        </article>

        <!-- 메모 -->
        <article class="panel">
          <h3>한가위 메모</h3>
          <label class="note">
            <!-- ✅ 부모보다 커지지 않도록 box-sizing 및 width 고정, 글자색 진하게 -->
            <textarea
                v-model="note"
                class="memo"
                placeholder="전하고 싶은 한마디를 적어보세요…"
            ></textarea>
          </label>
          <button class="btn ghost" @click="clearNote" :disabled="note.trim().length === 0">
            메모 지우기
          </button>

          <!-- 오방색 포인트 -->
          <div class="obang">
            <span class="c-blue" title="청(東)"></span>
            <span class="c-red" title="적(南)"></span>
            <span class="c-yellow" title="황(中)"></span>
            <span class="c-white" title="백(西)"></span>
            <span class="c-black" title="흑(北)"></span>
          </div>
        </article>
      </div>
    </section>

    <footer class="foot">
      <small>© 한가위 웹페이지 — 달/해 버튼으로 낮·밤 전환</small>
    </footer>

    <!-- 한지 텍스처 -->
    <div class="hanji" aria-hidden="true"></div>
  </main>
</template>

<script setup lang="ts">
import { ref } from 'vue';

/** 테마 토글 */
const isNight = ref(true);
function toggleTheme() {
  isNight.value = !isNight.value;
}

/** 데이터 타입 */
type Item = { name: string; desc: string; };

/** 추석 음식/놀이 */
const foods: Item[] = [
  { name: '송편', desc: '솔잎 향을 입힌 반달 떡. 수확과 소원을 빚어 넣어요.' },
  { name: '토란국', desc: '담백한 국물에 토란을 넣어 끓이는 제수 음식.' },
  { name: '전', desc: '호박·동태·버섯 등 재료를 노릇하게 지진 명절 부침.' },
  { name: '나물', desc: '고사리·시금치 등 산나물로 차례상과 식탁을 채워요.' },
  { name: '식혜', desc: '엿기름으로 만든 전통 음료. 달콤하고 깔끔한 맛.' },
];

const plays: Item[] = [
  { name: '강강술래', desc: '보름달 아래 손을 맞잡고 둥글게 돌며 추는 전통 춤.' },
  { name: '윷놀이', desc: '윷가락을 던져 말을 움직이는 가족 보드 게임.' },
  { name: '줄다리기', desc: '마을이 힘을 모아 승부를 겨루던 공동체 놀이.' },
  { name: '씨름', desc: '넉넉한 기운을 기원하며 겨루는 전통 민속 스포츠.' },
  { name: '소원 빌기', desc: '둥근 보름달을 바라보며 한 해의 소원을 되새겨요.' },
];

/** 메모 */
const note = ref('');
function clearNote() {
  note.value = '';
}

/** 풍등 스타일 (엄격 TS 안전) */
function lanternStyle(i: number) {
  const left = (i * 12 + 6) % 100;
  const delay = (i % 5) * 0.9;
  const dur = 10 + (i % 4) * 2;
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
/* 기본 레이아웃 / 컬러 */
.app {
  --bg-night: linear-gradient(180deg, #0b132b 0%, #1c2541 60%, #3a506b 100%);
  --bg-day: linear-gradient(180deg, #9be2ff 0%, #c7f5ff 60%, #fef6e4 100%);
  --ink: #1b1b1b;
  --ink-soft: #4a4a4a;
  --card: rgba(255,255,255,0.92);
  --card-border: rgba(0,0,0,0.08);

  min-height: 100svh;
  display: grid;
  grid-template-rows: auto 1fr auto;
  place-items: center;
  position: relative;
  overflow: hidden;
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

/* 별 */
.sky { position: absolute; inset: 0; overflow: hidden; pointer-events: none; }
.star {
  position: absolute; width: 2px; height: 2px; background: #fff9d6; border-radius: 50%;
  top: calc((var(--i, 1) * 7) % 100 * 1%); left: calc((var(--i, 1) * 13) % 100 * 1%);
  opacity: .85; animation: twinkle 2.8s infinite ease-in-out;
}
.star:nth-child(odd) { width: 1px; height: 1px; opacity: .6; }
.star:nth-child(3n) { animation-duration: 3.6s; }
.star:nth-child(n) { --i: 1; }
@keyframes twinkle { 0%,100%{opacity:.4;transform:scale(1)} 50%{opacity:1;transform:scale(1.6)} }
.app[data-night="false"] .star { display: none; }

/* 풍등 */
.lanterns { list-style: none; margin: 0; padding: 0; position: absolute; inset-inline: 0; bottom: -20vh; pointer-events: none; }
.lanterns li {
  position: absolute; bottom: -20vh; width: 26px; height: 34px;
  background: linear-gradient(#ffecd1, #ffc48a);
  border: 2px solid rgba(0,0,0,0.1);
  border-radius: 6px 6px 10px 10px;
  box-shadow: 0 8px 16px rgba(0,0,0,0.15), 0 0 20px rgba(255,160,64,0.45);
  animation: floatUp linear infinite;
}
.lanterns li::before, .lanterns li::after { content:''; position:absolute; left:50%; transform:translateX(-50%); }
.lanterns li::before { top:-10px; width:2px; height:14px; background:rgba(255,255,255,0.5); }
.lanterns li::after { bottom:-10px; width:6px; height:6px; background:#ffbe76; border-radius:50%; box-shadow:0 0 10px rgba(255,190,118,0.8); }
@keyframes floatUp { from{transform:translateY(0)} to{transform:translateY(-120vh)} }
.app[data-night="false"] .lanterns li { opacity:.75; box-shadow: 0 6px 12px rgba(0,0,0,0.1), 0 0 0 rgba(255,160,64,0); }

/* 헤더 */
.hero {
  width: 100%; max-width: 1040px;
  display: grid; grid-template-columns: 1fr auto;
  align-items: start; gap: 16px;
}
.copy h1 {
  margin: 0;
  font-size: clamp(26px, 5vw, 44px);
  font-weight: 800; letter-spacing: .01em;
  background: linear-gradient(90deg, #8b5cf6, #f59e0b, #ef4444);
  -webkit-background-clip: text; background-clip: text; color: transparent;
}
.eyebrow {
  display: inline-block; font-size: 12px; letter-spacing: .2em;
  padding: 4px 8px; border-radius: 999px;
  background: rgba(245,183,0,0.15); color: #a36b00; border: 1px solid rgba(245,183,0,0.35);
  margin-bottom: 8px;
}
.subtitle { margin: 8px 0 0; color: var(--ink-soft); }

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

/* 섹션 */
.section {
  width: 100%; max-width: 1040px;
  margin-top: clamp(16px, 3.5vw, 28px);
  background: var(--card);
  backdrop-filter: blur(8px);
  border: 1px solid var(--card-border);
  box-shadow: 0 18px 55px rgba(0,0,0,0.18);
  border-radius: 20px;
  padding: clamp(16px, 3.5vw, 28px);
}
.section > h2 { margin: 0 0 12px; font-size: clamp(22px, 3.6vw, 28px); }

/* 그리드 */
.grid { display: grid; gap: 14px; grid-template-columns: 1fr; }
@media (min-width: 860px) { .grid { grid-template-columns: 1.1fr 1fr 1.1fr; } }

/* 패널 */
.panel {
  background: #fff; border: 1px solid rgba(0,0,0,0.06);
  border-radius: 14px; padding: 14px;
  box-shadow: 0 8px 24px rgba(0,0,0,0.06);
  overflow: hidden; /* ✅ 내부 요소가 넘칠 때 잘림 방지 */
}
.panel h3 { margin: 0 0 8px; font-size: 16px; }

/* 리스트 */
.list { list-style: none; margin: 0; padding: 0; display: grid; gap: 8px; }
.list li { display: grid; grid-template-columns: auto auto 1fr; align-items: start; gap: 6px; }
.name { font-weight: 700; }
.dash { opacity: .6; }
.desc { color: var(--ink-soft); }

/* 메모 입력란 */
.note { display: block; }
.memo {
  width: 100%;
  max-width: 100%;
  min-height: 110px;
  resize: vertical;
  padding: 10px 12px;
  margin: 6px 0 0;
  border-radius: 10px;
  border: 1px solid rgba(0,0,0,0.12);
  background: #fffdfa;
  color: #111;                 /* ✅ 글씨 진하게 */
  box-sizing: border-box;      /* ✅ 패딩/보더 포함 → 부모보다 커지지 않음 */
  line-height: 1.45;
}
.memo::placeholder { color: rgba(0,0,0,0.45); } /* ✅ placeholder도 보이게 */

.btn {
  appearance: none; border: 1px solid rgba(0,0,0,0.15);
  background: transparent; color: var(--ink);
  padding: 8px 12px; border-radius: 10px; font-weight: 700;
  cursor: pointer; transition: transform 120ms ease, filter 120ms ease, background 120ms ease;
  margin-top: 10px;
}
.btn:hover { transform: translateY(-1px); background: rgba(0,0,0,0.04); }
.btn:active { transform: translateY(0); }

/* 오방색 */
.obang { display: grid; grid-template-columns: repeat(5, 1fr); gap: 6px; margin-top: 12px; }
.obang span { display:block; height: 16px; border-radius: 999px; box-shadow: inset 0 0 0 1px rgba(0,0,0,0.12); }
.c-blue { background:#0ea5e9; } .c-red{background:#ef4444;} .c-yellow{background:#fbbf24;}
.c-white{background:#e5e7eb;} .c-black{background:#111827; }

/* 푸터 */
.foot { margin-top: 12px; text-align: center; color: var(--ink-soft); }
</style>
