<script setup lang="ts">
import { Preferences } from '@capacitor/preferences';

const weatherInfos = ref();

const getPreviousWeather = async () => {
  setInterval(async () => {
    const { value } = await Preferences.get({ key: "lastWeather" });
    weatherInfos.value = value ? JSON.parse(value) : [];
  }, 1000);
};

const remove = async (consulted_at: string) => {
  const { value } = await Preferences.get({ key: "lastWeather" });
  const weatherInfos = value ? JSON.parse(value) : [];
  const newWeatherInfos = weatherInfos.filter((weather: any) => weather.consulted_at !== consulted_at);

  await Preferences.set({
    key: "lastWeather",
    value: JSON.stringify(newWeatherInfos)
  });
};

const goBack = async () => {
  await navigateTo("/");
};

onMounted(() => {
  getPreviousWeather();
});
</script>
<template>
  <ion-page>
    <ion-header>
      <ion-toolbar>
        <ion-title>Previsões Anteriores</ion-title>
      </ion-toolbar>
    </ion-header>
    <ion-content>
      <ion-list>
        <ion-item v-for="weather in weatherInfos" :key="weather.consulted_at">
          <ion-thumbnail>
            <ion-img :src="weather.icon" />
          </ion-thumbnail>
          <ion-label>
            <h2>{{ weather.condition }}</h2>
            <div class="flex flex-col">
              <span class="text-gray-500">Temperatura: {{ weather.temp_c }}°C</span>

              <span class="text-gray-500">Sensação Térmica: {{ weather.feelslike_c }}°C</span>

              <span class="text-gray-500">Consultado em: {{ weather.consulted_at }}</span>

              <span class="mt-2 bg-red-600 p-1 text-white rounded-md text-center"
                @click="remove(weather.consulted_at)">Remover registro</span>
            </div>
          </ion-label>
        </ion-item>
      </ion-list>
    </ion-content>
    <div class="w-full p-2">
      <ion-button class="w-full" @click="goBack()">Voltar</ion-button>
    </div>
  </ion-page>
</template>
