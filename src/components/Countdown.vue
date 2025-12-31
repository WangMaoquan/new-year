<script setup lang="ts">
  import { ref, onMounted, onUnmounted, computed } from 'vue';
  import Fireworks from './Fireworks.vue';

  const days = ref(0);
  const hours = ref(0);
  const minutes = ref(0);
  const seconds = ref(0);
  const isNewYear = ref(false);
  const soundEnabled = ref(true);

  let interval: number | null = null;

  const updateCountdown = () => {
    const now = new Date();
    const newYear = new Date('2026-01-01T00:00:00+08:00');
    const diff = newYear.getTime() - now.getTime();

    if (diff <= 0) {
      isNewYear.value = true;
      if (interval) clearInterval(interval);
      days.value = 0;
      hours.value = 0;
      minutes.value = 0;
      seconds.value = 0;
      return;
    }

    days.value = Math.floor(diff / (1000 * 60 * 60 * 24));
    hours.value = Math.floor((diff % (1000 * 60 * 60 * 24)) / (1000 * 60 * 60));
    minutes.value = Math.floor((diff % (1000 * 60 * 60)) / (1000 * 60));
    seconds.value = Math.floor((diff % (1000 * 60)) / 1000);
  };

  const formatNumber = (num: number): string => {
    return num.toString().padStart(2, '0');
  };

  const toggleSound = () => {
    soundEnabled.value = !soundEnabled.value;
  };

  onMounted(() => {
    updateCountdown();
    interval = setInterval(updateCountdown, 1000);
  });

  onUnmounted(() => {
    if (interval) clearInterval(interval);
  });

  const message = computed(() => {
    if (isNewYear.value) return '';
    if (days.value > 0) return '距离2026年还有';
    if (hours.value > 0) return '即将迎来新年';
    if (minutes.value > 0) return '新年倒计时';
    return '新年即将到来！';
  });
</script>

<template>
  <div
    class="relative w-full min-h-screen flex items-center justify-center overflow-hidden bg-linear-to-br from-[#0f0c29] via-[#302b63] to-[#24243e]"
  >
    <Fireworks v-if="isNewYear" />

    <div
      class="absolute top-0 left-0 w-full h-full overflow-hidden pointer-events-none"
    >
      <div
        v-for="i in 50"
        :key="i"
        class="absolute w-1 h-1 bg-[rgba(255,215,0,0.6)] rounded-full animate-float-up shadow-[0_0_10px_rgba(255,215,0,0.8)]"
        :style="{
          left: `${Math.random() * 100}%`,
          animationDelay: `${Math.random() * 20}s`,
          animationDuration: `${15 + Math.random() * 10}s`,
        }"
      ></div>
    </div>

    <div class="relative z-10 text-center p-8 max-md:p-4">
      <h1
        class="text-[clamp(4rem,15vw,12rem)] font-black m-0 bg-linear-to-tr from-[#ffd700] via-[#ffed4e] to-[#ffd700] bg-[length:200%_200%] bg-clip-text text-transparent animate-[gradient-shift_3s_ease_infinite,glow-pulse_2s_ease-in-out_infinite] drop-shadow-[0_0_40px_rgba(255,215,0,0.5)] tracking-[0.05em]"
      >
        {{ isNewYear ? '2026' : '2026' }}
      </h1>
      <p
        class="text-[clamp(1.5rem,4vw,2.5rem)] text-white/90 my-4 mb-12 font-light tracking-[0.3em] uppercase"
      >
        {{ isNewYear ? 'Happy New Year' : '新年倒计时' }}
      </p>

      <div
        v-if="!isNewYear"
        class="flex items-center justify-center gap-[clamp(1rem,3vw,2rem)] my-12 flex-wrap max-md:gap-2"
      >
        <div class="flex flex-col items-center gap-2">
          <div
            class="tabular-nums text-[clamp(3rem,8vw,6rem)] font-bold text-white bg-white/10 backdrop-blur-[10px] border-2 border-[rgba(255,215,0,0.3)] rounded-2xl px-[clamp(1.5rem,4vw,3rem)] py-[clamp(1rem,3vw,2rem)] min-w-[clamp(80px,20vw,140px)] shadow-[0_8px_32px_rgba(0,0,0,0.3),inset_0_0_20px_rgba(255,215,0,0.1)] animate-time-pulse"
          >
            {{ formatNumber(days) }}
          </div>
          <div
            class="text-[clamp(0.9rem,2vw,1.2rem)] text-[rgba(255,215,0,0.9)] font-medium uppercase tracking-[0.1em]"
          >
            天
          </div>
        </div>
        <div
          class="text-[clamp(2rem,6vw,4rem)] text-[rgba(255,215,0,0.6)] font-light -mx-2 animate-blink max-md:m-0"
        >
          :
        </div>
        <div class="flex flex-col items-center gap-2">
          <div
            class="tabular-nums text-[clamp(3rem,8vw,6rem)] font-bold text-white bg-white/10 backdrop-blur-[10px] border-2 border-[rgba(255,215,0,0.3)] rounded-2xl px-[clamp(1.5rem,4vw,3rem)] py-[clamp(1rem,3vw,2rem)] min-w-[clamp(80px,20vw,140px)] shadow-[0_8px_32px_rgba(0,0,0,0.3),inset_0_0_20px_rgba(255,215,0,0.1)] animate-time-pulse"
          >
            {{ formatNumber(hours) }}
          </div>
          <div
            class="text-[clamp(0.9rem,2vw,1.2rem)] text-[rgba(255,215,0,0.9)] font-medium uppercase tracking-[0.1em]"
          >
            时
          </div>
        </div>
        <div
          class="text-[clamp(2rem,6vw,4rem)] text-[rgba(255,215,0,0.6)] font-light -mx-2 animate-blink max-md:m-0"
        >
          :
        </div>
        <div class="flex flex-col items-center gap-2">
          <div
            class="tabular-nums text-[clamp(3rem,8vw,6rem)] font-bold text-white bg-white/10 backdrop-blur-[10px] border-2 border-[rgba(255,215,0,0.3)] rounded-2xl px-[clamp(1.5rem,4vw,3rem)] py-[clamp(1rem,3vw,2rem)] min-w-[clamp(80px,20vw,140px)] shadow-[0_8px_32px_rgba(0,0,0,0.3),inset_0_0_20px_rgba(255,215,0,0.1)] animate-time-pulse"
          >
            {{ formatNumber(minutes) }}
          </div>
          <div
            class="text-[clamp(0.9rem,2vw,1.2rem)] text-[rgba(255,215,0,0.9)] font-medium uppercase tracking-[0.1em]"
          >
            分
          </div>
        </div>
        <div
          class="text-[clamp(2rem,6vw,4rem)] text-[rgba(255,215,0,0.6)] font-light -mx-2 animate-blink max-md:m-0"
        >
          :
        </div>
        <div class="flex flex-col items-center gap-2">
          <div
            class="tabular-nums text-[clamp(3rem,8vw,6rem)] font-bold text-white bg-white/10 backdrop-blur-[10px] border-2 border-[rgba(255,215,0,0.3)] rounded-2xl px-[clamp(1.5rem,4vw,3rem)] py-[clamp(1rem,3vw,2rem)] min-w-[clamp(80px,20vw,140px)] shadow-[0_8px_32px_rgba(0,0,0,0.3),inset_0_0_20px_rgba(255,215,0,0.1)] animate-time-pulse"
          >
            {{ formatNumber(seconds) }}
          </div>
          <div
            class="text-[clamp(0.9rem,2vw,1.2rem)] text-[rgba(255,215,0,0.9)] font-medium uppercase tracking-[0.1em]"
          >
            秒
          </div>
        </div>
      </div>

      <div
        class="text-[clamp(1.2rem,3vw,1.8rem)] text-white/80 my-8 font-light tracking-[0.05em]"
      >
        {{ message }}
      </div>

      <div v-if="isNewYear" class="mt-12 animate-fade-in-up">
        <p
          class="text-[clamp(2rem,5vw,3.5rem)] font-bold m-0 mb-4 bg-linear-to-r from-[#ff6b6b] via-[#ffd93d] to-[#ff6bff] bg-size-[200%_200%] bg-clip-text text-transparent animate-rainbow"
        >
          🎊 新年快乐！Happy New Year! 🎊
        </p>
        <p class="text-[clamp(1rem,2.5vw,1.5rem)] text-white/80 font-light m-0">
          愿2026年充满欢乐、健康与成功
        </p>
      </div>
    </div>

    <button
      class="fixed bottom-8 right-8 max-md:bottom-4 max-md:right-4 w-15 h-15 max-md:w-12.5 max-md:h-12.5 rounded-full border-2 border-[rgba(255,215,0,0.3)] bg-white/10 backdrop-blur-[10px] cursor-pointer flex items-center justify-center transition-all duration-300 z-100 hover:scale-110 hover:border-[rgba(255,215,0,0.6)] hover:shadow-[0_0_20px_rgba(255,215,0,0.4)]"
      @click="toggleSound"
      :title="soundEnabled ? '关闭音效' : '开启音效'"
    >
      <span class="text-[1.5rem]">{{ soundEnabled ? '🔊' : '🔇' }}</span>
    </button>
  </div>
</template>
