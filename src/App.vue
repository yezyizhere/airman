<template>
  <div class="h-screen flex items-center justify-center bg-blue-300">
    <div class="text-center">
      <h1 class="text-2xl font-bold mb-4">싹지의 슬기로운 병영생활</h1>

      <div class="flex justify-center mb-2">
        <h2 class="text-4xl font-bold">D -</h2>
        <h2 class="ml-3 text-4xl font-bold text-red-700">{{ daysText }}</h2>
      </div>

      <div class="flex justify-center mt-5 mb-4">
        <h3 class="text-5xl font-bold">{{ hoursText[0] }}</h3>
        <h3 class="ml-3 text-5xl font-bold">{{ hoursText[1] }}</h3>
        <div class="ml-3 text-5xl font-bold">:</div>
        <h4 class="ml-3 text-5xl font-bold">{{ minutesText[0] }}</h4>
        <h4 class="ml-3 text-5xl font-bold">{{ minutesText[1] }}</h4>
        <div class="ml-3 text-5xl font-bold">:</div>
        <h5 class="ml-3 text-5xl font-bold">{{ secondsText[0] }}</h5>
        <h5 class="ml-3 text-5xl font-bold">{{ secondsText[1] }}</h5>
      </div>

      <p class="mt-5 text-2xl">{{ currentContent }}</p>

      <!-- 요청하신 하단 UI 추가 -->
      <div class="flex w-full justify-center text-lg mt-3">
        <p class="mr-3">전역까지.. {{ remainingDays }}일</p>
        <p>{{ progressRate }}%</p>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from "vue";

const timerData = {
  "2025-08-15 00:00:00": { content: "광복절" },
  "2025-10-01 00:00:00": { content: "경) 상병 진급 (축" },
  "2025-10-03 00:00:00": { content: "7일의 공휴일 시작" },
  "2025-10-10 00:00:00": { content: "하루는 못 쉼" },
  "2025-12-25 00:00:00": { content: "군대 크리스마스ㅋㅋ" },
  "2026-01-01 00:00:00": { content: "새드뉴이어2" },
  "2026-02-14 00:00:00": { content: "설날" },
  "2026-03-01 00:00:00": { content: "삼일절" },
  "2026-04-01 00:00:00": { content: "경) 병장 진급 (축" },
  "2026-05-05 00:00:00": { content: "건방지게 어른이 어린이날에 쉰다니" },
  "2026-06-06 00:00:00": { content: "현충일; 토요일의 공휴일" },
  "2026-08-15 00:00:00": { content: "광복절; 토요일의 공휴일 시즌2" },
  "2026-09-22 07:00:00": { content: "전역 까지" },
};

// 현재 날짜 이후의 날짜만 필터링
const now = new Date();
const allDates = Object.keys(timerData).sort();
const filteredDates = allDates.filter((dateStr) => new Date(dateStr) > now).sort();

// 전체 기간 계산용: 첫 번째 날짜(입대일)와 마지막 날짜(전역일)
const firstDay = new Date("2024-12-23 14:00:00");
const lastDay = new Date("2026-09-22 07:00:00");
const totalDays = Math.ceil((lastDay - firstDay) / (1000 * 60 * 60 * 24));

const currentIndex = ref(0);
const targetDate = ref(filteredDates.length ? new Date(filteredDates[0]) : null);
const days = ref(0);
const hours = ref(0);
const minutes = ref(0);
const seconds = ref(0);
const currentContent = ref(filteredDates.length ? timerData[filteredDates[0]].content : "카운트다운 종료");

const updateCountdown = () => {
  const now = new Date();
  if (!targetDate.value) {
    days.value = 0;
    hours.value = 0;
    minutes.value = 0;
    seconds.value = 0;
    currentContent.value = "카운트다운 종료";
    return;
  }

  const timeDiff = targetDate.value - now;
  if (timeDiff < 0) {
    currentIndex.value++;
    if (currentIndex.value >= filteredDates.length) {
      days.value = 0;
      hours.value = 0;
      minutes.value = 0;
      seconds.value = 0;
      currentContent.value = "카운트다운 종료";
      return;
    }
    targetDate.value = new Date(filteredDates[currentIndex.value]);
    currentContent.value = timerData[filteredDates[currentIndex.value]].content;
    return;
  }

  days.value = Math.floor(timeDiff / (1000 * 60 * 60 * 24));
  hours.value = Math.floor((timeDiff % (1000 * 60 * 60 * 24)) / (1000 * 60 * 60));
  minutes.value = Math.floor((timeDiff % (1000 * 60 * 60)) / (1000 * 60));
  seconds.value = Math.floor((timeDiff % (1000 * 60)) / 1000);
};

// 남은 일수(전역일 기준)
const remainingDays = computed(() => {
  const now = new Date();
  const diff = lastDay - now;
  return Math.max(Math.ceil(diff / (1000 * 60 * 60 * 24)), 0);
});

// 진행률(퍼센트)
const progressRate = computed(() => {
  const now = new Date();
  const elapsed = now - firstDay;
  const total = lastDay - firstDay;
  if (total <= 0 || elapsed < 0) return 0;
  return Math.min(Math.floor((elapsed / total) * 100), 100);
});

const daysText = computed(() => String(days.value).padStart(2, "0"));
const hoursText = computed(() => String(hours.value).padStart(2, "0").split(""));
const minutesText = computed(() => String(minutes.value).padStart(2, "0").split(""));
const secondsText = computed(() => String(seconds.value).padStart(2, "0").split(""));

onMounted(() => {
  updateCountdown();
  setInterval(updateCountdown, 1000);
});
</script>
