<template>
  <div class="relative min-h-screen bg-gradient-to-b from-yellow-100 via-orange-100 to-orange-200 overflow-hidden flex flex-col items-center justify-center p-6">
    <!-- 달 배경 -->
    <div class="absolute w-96 h-96 rounded-full bg-yellow-200 opacity-30 top-1/2 left-1/2 transform -translate-x-1/2 -translate-y-1/2 shadow-2xl animate-pulse"></div>

    <!-- 타이틀 -->
    <h1 class="relative text-5xl md:text-7xl font-extrabold text-gray-800 mb-4 z-10 text-center">
      풍성한 한가위 🌕
    </h1>

    <!-- 안내 메시지 -->
    <p class="relative text-lg md:text-2xl text-gray-700 mb-6 text-center max-w-xl z-10">
      덕담을 입력하고 버튼을 눌러 연등을 올려보세요!
    </p>

    <!-- 입력창 -->
    <div class="relative flex gap-2 mb-6 z-10">
      <input
          v-model="inputMessage"
          type="text"
          placeholder="덕담 입력..."
          class="px-4 py-2 rounded-lg border border-gray-300 focus:outline-none focus:ring-2 focus:ring-orange-400"
          @keyup.enter="addLantern"
      />
      <button
          class="px-4 py-2 bg-orange-400 text-white rounded-lg shadow-lg hover:bg-orange-500 transition-all duration-300"
          @click="addLantern"
      >
        연등 올리기
      </button>
    </div>

    <!-- 연등들 -->
    <div class="absolute bottom-0 w-full h-full pointer-events-none">
      <div
          v-for="(lantern, index) in lanterns"
          :key="index"
          class="absolute w-24 h-12 bg-pink-300 rounded-full text-center text-white font-bold flex items-center justify-center shadow-lg animate-rise"
          :style="{
          left: lantern.x + '%',
          animationDuration: lantern.duration + 's',
          animationDelay: lantern.delay + 's',
          transform: 'rotate(' + lantern.rotation + 'deg)'
        }"
      >
        {{ lantern.message }}
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref } from 'vue';

interface Lantern {
  x: number;
  delay: number;
  duration: number;
  message: string;
  rotation: number;
}

export default defineComponent({
  name: 'App',
  setup() {
    const inputMessage = ref('');
    const lanterns = ref<Lantern[]>([]);

    const addLantern = () => {
      const message = inputMessage.value.trim();
      if (!message) return;

      const newLantern: Lantern = {
        x: Math.random() * 80 + 10,       // 좌우 위치
        delay: Math.random() * 2,         // 애니메이션 지연
        duration: Math.random() * 3 + 4,  // 애니메이션 속도 랜덤 (4~7초)
        rotation: Math.random() * 360,    // 초기 회전
        message,
      };
      lanterns.value.push(newLantern);
      inputMessage.value = '';

      // 연등 사라지게 duration 후 제거
      setTimeout(() => {
        lanterns.value.shift();
      }, newLantern.duration * 1000);
    };

    return { inputMessage, lanterns, addLantern };
  },
});
</script>

<style scoped>
/* 달 빛나는 애니메이션 */
@keyframes pulse {
  0%, 100% { transform: scale(1); opacity: 0.3; }
  50% { transform: scale(1.05); opacity: 0.5; }
}
.animate-pulse {
  animation: pulse 4s ease-in-out infinite;
}

/* 연등 상승 애니메이션 */
@keyframes rise {
  0% { transform: translateY(0) rotate(0deg); opacity: 1; }
  100% { transform: translateY(-600px) rotate(360deg); opacity: 0; }
}
.animate-rise {
  animation-name: rise;
  animation-timing-function: linear;
  animation-fill-mode: forwards;
}
</style>
