<script setup lang="ts">
import { Preferences } from '@capacitor/preferences';

const weatherInfos = ref();

const getPreviousWeather = async () => {
  const { value } = await Preferences.get({ key: "lastWeather" });
  weatherInfos.value = value ? JSON.parse(value) : [];
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
        <ion-item v-for="weather in weatherInfos" :key="weather.last_updated">
          <ion-thumbnail>
            <ion-img :src="weather.icon" />
          </ion-thumbnail>
          <ion-label>
            <h2>{{ weather.condition }}</h2>
            <p>
              Temperatura: {{ weather.temp_c }}°C
              <br />
              Sensação Térmica: {{ weather.feelslike_c }}°C
              <br />
              Atualizado em: {{ weather.last_updated }}
            </p>
          </ion-label>
        </ion-item>
      </ion-list>
    </ion-content>
    
  </ion-page>
</template>
