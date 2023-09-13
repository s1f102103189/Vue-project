<template>
  <div :class="weatherClass">
    <div class="search-box">
      <input v-model="city" placeholder="都市名を入力してください" />
      <button @click="refreshWeather">天気を更新</button>
    </div>

    <!-- 検索履歴を表示 -->
    <div v-if="searchHistory.length">
      <h3>検索履歴</h3>
      <ul>
        <li v-for="historyCity in searchHistory" :key="historyCity" @click="city = historyCity">
          {{ historyCity }}
        </li>
      </ul>
    </div>

    <!-- ローディング表示 -->
    <div v-if="loading">
      <p>Loading...</p>
    </div>

    <!-- 天気情報を表示 -->
    <transition name="fade">
      <div v-if="weather">
        <h2>{{ city }} の天気:</h2>
        <img :src="weatherIcon" alt="Weather Icon" />
        <p :style="weatherTextStyle">気温: {{ weather.temperature }}°C</p>
        <p :style="weatherTextStyle">状況: {{ weather.condition }}</p>
      </div>
    </transition>

    <!-- エラーメッセージを表示 -->
    <div v-if="error" class="error-message">
      <p>{{ error }}</p>
    </div>
  </div>
</template>

<script>
import { ref, watch, computed } from 'vue';

export default {
  name: 'WeatherApp',
  setup() {
    const city = ref('');
    const weather = ref(null);
    const error = ref(null);
    const loading = ref(false);
    const searchHistory = ref([]);

    const fetchWeatherForCity = async (cityName) => {
      // こちらはダミーデータを使用しています。
      // 実際には、APIを呼び出してデータを取得する必要があります。
      return {
        temperature: 20,
        condition: '晴れ'
      };
    };

    const refreshWeather = async () => {
      if (city.value.trim() !== '') {
        try {
          loading.value = true;
          weather.value = await fetchWeatherForCity(city.value);
          error.value = null;
          if (!searchHistory.value.includes(city.value)) {
            searchHistory.value.push(city.value);
          }
        } catch (err) {
          error.value = '天気情報の取得に失敗しました。';
        } finally {
          loading.value = false;
        }
      } else {
        error.value = '都市名を正しく入力してください。';
      }
    };

    const weatherIcon = computed(() => {
      if (weather.value) {
        switch (weather.value.condition) {
          case '晴れ':
            return '/path/to/sunny-icon.png';
          default:
            return '';
        }
      }
      return '';
    });

    const weatherTextStyle = computed(() => {
      if (weather.value) {
        switch (weather.value.condition) {
          case '晴れ':
            return { color: 'blue' };
          default:
            return {};
        }
      }
      return {};
    });

    const weatherClass = computed(() => {
      if (weather.value) {
        switch (weather.value.condition) {
          case '晴れ':
            return 'sunny';
          default:
            return '';
        }
      }
      return '';
    });

    let debounce;
    watch(city, (newCity, oldCity) => {
      clearTimeout(debounce);
      if (newCity !== oldCity && newCity.trim() !== '') {
        debounce = setTimeout(refreshWeather, 500);
      }
    });

    return {
      city,
      weather,
      error,
      loading,
      searchHistory,
      refreshWeather,
      weatherIcon,
      weatherTextStyle,
      weatherClass
    };
  }
};
</script>

<style>
.search-box {
  display: flex;
  gap: 10px;
  margin-bottom: 20px;
}
.error-message {
  color: red;
  font-weight: bold;
  border: 1px solid red;
  padding: 10px;
  margin-top: 10px;
}
.fade-enter-active, .fade-leave-active {
  transition: opacity 0.5s;
}
.fade-enter, .fade-leave-to {
  opacity: 0;
}
.sunny {
  background-color: #FFEB3B;
}
</style>
