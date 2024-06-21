# ClimaTempo com Ionic + Nuxt

## Objetivo da aplicação

> O aplicativo deve permitir ao usuário rastrear as condições meteorológicas de diferentes locais com base na geolocalização e armazenar as informações de consulta para acesso futuro.

# REQUISITOS DO APLICATIVO: 

1. Geolocalização: - O aplicativo deve solicitar permissão do usuário para acessar a localização atual. - Utilizar a API de geolocalização do Capacitor para obter as coordenadas (latitude e longitude) 
atuais do usuário. 

2. API REST: - Consumir uma API REST de informações meteorológicas (por exemplo, OpenWeatherMap ou 
WeatherAPI) para obter os dados meteorológicos com base nas coordenadas obtidas. - A API deve fornecer informações como temperatura, umidade, descrição do tempo e ícone 
representativo das condições meteorológicas. 

3. Storage: - Utilizar o Capacitor Storage para armazenar localmente as consultas realizadas, incluindo as 
informações meteorológicas e a data/hora da consulta. - Implementar uma lista de histórico de consultas armazenadas, permitindo ao usuário 
visualizar os dados meteorológicos de consultas anteriores.

# FUNCIONALIDADES DETALHADAS: 

1. Tela Principal: - Botão "Obter Localização Atual" que, ao ser clicado, obtém a localização do usuário e exibe 
as informações meteorológicas atuais. - Mostrar dados como temperatura, umidade, descrição do tempo e um ícone representativo 
do tempo atual. 

2. Histórico de Consultas: - Exibir uma lista das consultas anteriores armazenadas. - Cada item da lista deve mostrar a data/hora da consulta e um resumo das condições 
meteorológicas (por exemplo, "12/06/2024 14:30 - Temp: 25°C, Ensolarado").

# Como rodar o projeto

```bash
# Clone o repositório

$ git clone https://github.com/andrefelipe18/ClimaTempo.git climatempo

# Acesse a pasta do projeto no terminal/cmd

$ cd climatempo

# Instale as dependências

$ npm install

# Execute o projeto

$ npx ionic cap run android --livereload --external # Para rodar no Android

$ npx ionic cap run ios --livereload --external # Para rodar no IOS

# O aplicativo será aberto no emulador ou no dispositivo conectado automaticamente, com o live reload ativado
```
