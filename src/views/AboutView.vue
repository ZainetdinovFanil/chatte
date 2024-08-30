<template>
  <div class="success-container">
    <div v-if="loading" class="spinner"></div>
    <div v-else>
      <h1>Форма отправлена!</h1>
      <p>Текст: {{ textInput }}</p>
      <p>Код: {{ otpInput }}</p>
      <p v-if="files">Файлы (файл): {{ files.map((file) => file.name).join(", ") }}</p>
      <div class="map-container">
        <GoogleMap
          class="map"
          :center="center"
          :zoom="zoom"
        >
        </GoogleMap>
      </div>
    </div>
  </div>
</template>

<script>
import { GoogleMap } from "vue3-google-map";
import { Geolocation } from '@capacitor/geolocation';

export default {
  components: {
    GoogleMap,
  },
  data() {
    return {
      loading: true,
      textInput: "",
      otpInput: "",
      files: [],
      center: { lat: 40.689247, lng: -74.044502 },
      zoom: 12,
    };
  },
  mounted() {
    setTimeout(() => {
      this.loading = false;
      this.textInput = this.$route.query.textInput;
      this.otpInput = this.$route.query.otpInput;
      this.files = JSON.parse(this.$route.query.files);
      this.getGeolocation();
    }, 1000);
  },
  methods: {
    async getGeolocation() {
      try {
        const coordinates = await Geolocation.getCurrentPosition();
        this.center = {
          lat: coordinates.coords.latitude,
          lng: coordinates.coords.longitude,
        };
      } catch (error) {
        console.error('Error getting location', error);
      }
    },
  },
};
</script>

<style lang="less" scoped>
.success-container {
  text-align: center;
  margin-top: 50px;
  height: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
}

.spinner {
  border: 5px solid #f3f3f3;
  border-top: 5px solid #3498db;
  border-radius: 50%;
  width: 120px;
  height: 120px;
  animation: spin 2s linear infinite;
  margin: 0 auto;
}

@keyframes spin {
  0% {
    transform: rotate(0deg);
  }
  100% {
    transform: rotate(360deg);
  }
}

.map-container {
  margin-top: 20px;
  height: 500px;
}
.map {
  height: 100%;
  width: 100%;
}
</style>
