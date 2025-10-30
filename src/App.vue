<template>
  <main class="relative min-h-screen font-sans text-white overflow-x-hidden">
    <!-- 배경 -->
    <div class="absolute inset-0 bg-gradient-to-b from-yellow-50 via-orange-100 to-orange-200 -z-10 overflow-hidden">
      <!-- 달빛 -->
      <div class="absolute w-96 h-96 rounded-full bg-yellow-200 opacity-30 top-1/2 left-1/2 transform -translate-x-1/2 -translate-y-1/2 shadow-2xl animate-pulse"></div>

      <!-- 별 -->
      <div class="absolute w-full h-full">
        <div class="absolute w-1 h-1 bg-white opacity-50 rounded-full top-10 left-20 animate-twinkle"></div>
        <div class="absolute w-1 h-1 bg-white opacity-40 rounded-full top-32 left-60 animate-twinkle animation-delay-500"></div>
        <div class="absolute w-1 h-1 bg-white opacity-60 rounded-full top-48 left-30 animate-twinkle animation-delay-1000"></div>
      </div>

      <!-- 구름 -->
      <div class="absolute w-72 h-24 bg-white/20 rounded-full top-1/4 left-1/6 blur-md animate-cloud"></div>
      <div class="absolute w-96 h-28 bg-white/15 rounded-full top-1/3 left-2/3 blur-md animate-cloud animation-delay-2000"></div>
    </div>

    <!-- 달 & 토끼 -->
    <section class="relative flex flex-col items-center text-center pt-24">
      <div class="moon relative w-72 h-72 md:w-96 md:h-96 mb-6">
        <svg class="rabbit absolute w-2/3 left-1/2 top-1/2 -translate-x-1/2 -translate-y-1/2 rotate-[-8deg] opacity-50" viewBox="0 0 120 120">
          <path d="M78 48c6-7 4-17-3-22-6-4-14-3-19 2 1-7-3-14-9-17-8-4-18 0-22 9-4 8-1 17 6 22-8 5-13 14-13 24 0 16 14 29 31 29s31-13 31-29c0-6-2-12-6-18l4 0z" fill="#a15e2a"/>
          <circle cx="50" cy="70" r="6" fill="#ffecc9" opacity="0.5"/>
        </svg>
      </div>
      <h1 class="text-3xl md:text-5xl font-extrabold mb-2 text-shadow">풍성한 한가위<br>마음도 가득 찼으면</h1>

      <!-- 버튼 -->
      <div class="flex gap-4 mb-12">
        <button class="px-6 py-2 bg-yellow-400 text-black rounded-full font-bold shadow hover:bg-yellow-500 transition" @click="lanternOn = !lanternOn">
          {{ lanternOn ? '연등 끄기' : '연등 켜기' }}
        </button>
        <button class="px-6 py-2 border border-yellow-400 text-yellow-400 rounded-full font-bold shadow hover:bg-yellow-400 hover:text-black transition" @click="scrollToWishes">
          덕담 남기기
        </button>
      </div>
    </section>

    <!-- 연등 -->
    <transition-group name="lantern" tag="div" class="relative h-64 pointer-events-none" v-if="lanternOn">
      <div v-for="n in 6" :key="n" :style="lanternStyle(n)" class="absolute w-12 h-16 bg-pink-300 rounded-lg flex items-center justify-center shadow-lg lantern-body">
        <div class="lantern-light"></div>
      </div>
    </transition-group>

    <!-- 덕담 섹션 -->
    <section class="wishes px-4 md:px-0 max-w-xl mx-auto py-12" ref="wishesRef">
      <h2 class="text-2xl font-bold mb-4">오늘의 덕담</h2>
      <form class="flex gap-2 mb-4" @submit.prevent="addWish">
        <input v-model.trim="draft" type="text" placeholder="예) 가족 모두 건강하고, 하는 일마다 술술 풀리길!" class="flex-1 px-4 py-2 rounded-lg bg-white/10 text-white placeholder-white/60 focus:outline-none focus:ring-2 focus:ring-yellow-400"/>
        <button class="px-4 py-2 bg-yellow-400 text-black rounded-lg font-bold shadow disabled:opacity-50" :disabled="!draft">남기기</button>
      </form>

      <ul v-if="wishes.length" class="space-y-2">
        <li v-for="(w, i) in wishes" :key="w.id" class="flex items-center justify-between bg-white/10 px-4 py-2 rounded-lg">
          <span>🌕 {{ w.text }}</span>
          <button @click="removeWish(i)" class="text-white/70 hover:text-white">×</button>
        </li>
      </ul>
      <p v-else class="text-white/50">아직 덕담이 없어요. 첫 덕담을 남겨보세요!</p>
    </section>

    <!-- Footer -->
    <footer class="text-center py-6 text-white/70">© {{ new Date().getFullYear() }} 한가위 웹 · 만든이: 당신 💛</footer>
  </main>
</template>

<script setup lang="ts">
import { ref } from 'vue'

type Wish = { id: number; text: string }

const lanternOn = ref(true)
const wishes = ref<Wish[]>([
  { id: 1, text: '보름달처럼 넉넉한 마음 가득하시길!' },
  { id: 2, text: '멀리 있어도 마음만은 한가위에 함께해요.' },
])
const draft = ref('')
const wishesRef = ref<HTMLElement | null>(null)

function lanternStyle(n: number) {
  const delay = (n * 0.6) + 's'
  const left = (10 + n * 12) % 90
  const scale = 0.8 + (n % 3) * 0.1
  return {
    left: `${left}%`,
    bottom: '-64px',
    animation: `rise 14s linear ${delay} infinite`,
    transform: `scale(${scale})`,
  }
}

function scrollToWishes() {
  wishesRef.value?.scrollIntoView({ behavior: 'smooth', block: 'start' })
}

function addWish() {
  if (!draft.value) return
  wishes.value.unshift({ id: Date.now(), text: draft.value })
  draft.value = ''
}

function removeWish(index: number) {
  wishes.value.splice(index, 1)
}
</script>

<style scoped>
/* 달빛 애니메이션 */
@keyframes pulse {
  0%, 100% { transform: scale(1); opacity: 0.3; }
  50% { transform: scale(1.05); opacity: 0.5; }
}
.animate-pulse {
  animation: pulse 6s ease-in-out infinite;
}

/* 별 반짝임 */
@keyframes twinkle {
  0%, 100% { opacity: 0.3; }
  50% { opacity: 1; }
}
.animate-twinkle {
  animation: twinkle 4s ease-in-out infinite;
}
.animation-delay-500 { animation-delay: 0.5s; }
.animation-delay-1000 { animation-delay: 1s; }

/* 구름 애니메이션 */
@keyframes cloud {
  0% { transform: translateX(0); }
  100% { transform: translateX(120vw); }
}
.animate-cloud {
  animation: cloud 40s linear infinite;
}
.animation-delay-2000 { animation-delay: 2s; }

/* 연등 상승 */
@keyframes rise {
  0% { transform: translateY(0); opacity: 1; }
  100% { transform: translateY(-28rem); opacity: 0; }
}

/* 달 & 토끼 */
.moon {
  border-radius: 50%;
  background: radial-gradient(circle at 35% 35%, #fff5d6 0%, #ffe5a8 40%, #ffd27d 60%, #fbc266 70%, #eaa95a 100%);
  box-shadow:
      0 0 40px 10px #ffd27d55,
      inset -8px -12px 30px #00000022;
}
.rabbit {
  filter: blur(0.2px);
}

/* 텍스트 그림자 */
.text-shadow {
  text-shadow: 0 2px 18px rgba(255, 214, 125, 0.25);
}
</style>
