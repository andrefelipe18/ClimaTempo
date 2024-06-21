<script setup lang="ts">
import { Geolocation } from '@capacitor/geolocation';
import { Preferences } from '@capacitor/preferences';
import type FormattedWeather from '@/types/FormattedWeather';

const coordinates = ref();
const weatherInfos = ref();
const config = useRuntimeConfig()

async function getLocation () {
  coordinates.value = (await Geolocation.getCurrentPosition()).coords;

  await Preferences.set({
    key: "coords",
    value: JSON.stringify(coordinates.value)
  });

  weatherInfos.value = await getWeather();
}

async function getCoords() {
  const prefs = await Preferences.get({ key: "coords" });
  return prefs.value ? JSON.parse(prefs.value) : null;
}

function buildWeatherApiUrl(apiKey: string, latitude: string, longitude: string) {
  return `https://api.weatherapi.com/v1/current.json?key=${apiKey}&q=${latitude},${longitude}&lang=pt`;
}

async function storeWeatherData(formattedData: FormattedWeather) {
  const { value } = await Preferences.get({ key: "lastWeather" });
  const weatherData = value ? [...JSON.parse(value), formattedData] : [formattedData];
  await Preferences.set({
    key: "lastWeather",
    value: JSON.stringify(weatherData)
  });
}

async function getWeather() {
  const coords = await getCoords();
  if (!coords) return null; // Handle error or no coords case

  const { latitude, longitude } = coords;
  const apiKey = config.public.apiSecret;
  const url = buildWeatherApiUrl(apiKey, latitude, longitude);

  const response = await fetch(url);
  const data = await response.json();

  const formattedData = {
    condition: data.current.condition.text,
    icon: data.current.condition.icon,
    temp_c: data.current.temp_c,
    feelslike_c: data.current.feelslike_c,
    last_updated: data.current.last_updated,
    consulted_at: new Date().toLocaleString("pt-BR", {
      day: "2-digit",
      month: "2-digit",
      year: "numeric",
      hour: "2-digit",
      minute: "2-digit",
      second: "2-digit"
    })
  };

  await storeWeatherData(formattedData);

  return data.current;
}

async function previousWeather () {
  await navigateTo("/history");
}

</script>

<template>
  <ion-page>
    <Header title="ClimaTempo App (Nuxt + Ionic)" />

    <ion-content>
      <ion-item v-if="weatherInfos">
        <ion-thumbnail>
          <ion-img :src="weatherInfos.condition.icon" />
        </ion-thumbnail>
        <ion-label>
          <h2>{{ weatherInfos.condition.text }}</h2>
          <p>
            Temperatura: {{ weatherInfos.temp_c }}°C
            <br>
            Sensação Térmica: {{ weatherInfos.feelslike_c }}°C
            <br>
            Atualizado em: {{ weatherInfos.last_updated }}
          </p>
        </ion-label>
      </ion-item>
      <ion-item v-else>
        <ion-label>
          <h2>Obtenha o clima</h2>
        </ion-label>
      </ion-item>
    </ion-content>

    <div class="w-full p-2">
      <ion-button class="w-full" @click="previousWeather()">Histórico</ion-button>
      <ion-button class="w-full" @click="getLocation()">Obter Clima</ion-button>
    </div>
  </ion-page>
</template>
