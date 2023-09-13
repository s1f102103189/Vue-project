<template>
  <div>
    <input v-model="city" placeholder="都市名を入力してください" />
    <div v-if="weather">
      <h2>{{ city }} の天気:</h2>
      <p>気温: {{ weather.temperature }}°C</p>
      <p>状況: {{ weather.condition }}</p>
    </div>
  </div>
</template>

<script>
import { ref, watch } from 'vue';

export default {
  name: 'WeatherApp',
  setup() {
    const city = ref('');
    const weather = ref(null);

    // この関数は、実際のAPIを使用して天気をフェッチします。
    // しかし、このコード例ではインターネットアクセスは無効なため、
    // ダミーデータを使用しています。
    const fetchWeatherForCity = async (cityName) => {
      // ここで実際のAPIを呼び出すことができます。
      // 例: `http://api.weatherapi.com/v1/current.json?key=YOUR_KEY&q=${cityName}`

      // ダミーデータ
      return {
        temperature: 20,
        condition: '晴れ'
      };
    };

    // 都市名が変更されたときに天気をフェッチします
    watch(city, async (newCity, oldCity) => {
      if (newCity !== oldCity && newCity.trim() !== '') {
        weather.value = await fetchWeatherForCity(newCity);
      }
    });

    return {
      city,
      weather
    };
  }
};
</script>
