<script setup lang="ts">
import { Geolocation } from '@capacitor/geolocation';
import { Preferences } from '@capacitor/preferences';

const coordinates = ref();
const latitude = ref();
const longitude = ref();
const weatherInfos = ref();

const getLocation = async () => {
  coordinates.value = (await Geolocation.getCurrentPosition()).coords;

  await Preferences.set({
    key: "coords",
    value: JSON.stringify(coordinates.value)
  });

  weatherInfos.value = await getWeather();
};

const getWeather = async () => {
  const prefs = await Preferences.get({ key: "coords" });
  const coords = prefs.value ? JSON.parse(prefs.value) : null;
  latitude.value = coords.latitude;
  longitude.value = coords.longitude;

  let url = `https://api.weatherapi.com/v1/current.json?key=f133019b1dac470ba92141812242106&q=${latitude.value},${longitude.value}&lang=pt`;

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

  const { value } = await Preferences.get({ key: "lastWeather" });

  if (value) {
    await Preferences.set({
      key: "lastWeather",
      value: JSON.stringify([...JSON.parse(value), formattedData])
    });
  } else {
    await Preferences.set({
      key: "lastWeather",
      value: JSON.stringify([formattedData])
    });
  }

  return data.current;
};

const previousWeather = async () => {
  await navigateTo("/history");
};

</script>
<template>
  <ion-page>
    <ion-header>
      <ion-toolbar>
        <ion-title>ClimaTempo App (Nuxt + Ionic)</ion-title>
      </ion-toolbar>
    </ion-header>

    <ion-content>
      <ion-item v-if="weatherInfos">
        <ion-thumbnail>
          <ion-img :src="weatherInfos.condition.icon" />
        </ion-thumbnail>
        <ion-label>
          <h2>{{ weatherInfos.condition.text }}</h2>
          <p>
            Temperatura: {{ weatherInfos.temp_c }}°C
            <br />
            Sensação Térmica: {{ weatherInfos.feelslike_c }}°C
            <br />
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
