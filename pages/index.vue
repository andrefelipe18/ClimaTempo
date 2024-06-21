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
    last_updated: data.current.last_updated
  };

  const { value } = await Preferences.get({ key: "lastWeather" });

  debug.value = JSON.stringify(value);

  //Se já existir um valor, adiciona mais um
  if (value) {
    await Preferences.set({
      key: "lastWeather",
      value: JSON.stringify([...JSON.parse(value), formattedData])
    });
  } else {
    //Se não existir cria um array com o valor
    await Preferences.set({
      key: "lastWeather",
      value: JSON.stringify([formattedData])
    });
  }

  return data.current;
};

const previousWeather = async () => {
  await navigateTo("/previousweathers");
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
      <ion-list>
        <ion-item v-if="weatherInfos">
          <ion-thumbnail>
            <ion-img :src="weatherInfos.icon" />
          </ion-thumbnail>
          <ion-label>
            <h2>{{ weatherInfos.condition }}</h2>
            <p>
              Temperatura: {{ weatherInfos.temp_c }}°C
              <br />
              Sensação Térmica: {{ weatherInfos.feelslike_c }}°C
              <br />
              Atualizado em: {{ weatherInfos.last_updated }}
            </p>
          </ion-label>
        </ion-item>
      </ion-list>
    </ion-content>
    <ion-button @click="getLocation">Obter Clima</ion-button>
  </ion-page>
</template>
