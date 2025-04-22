<template>
  <ion-page>
    <Navbar title="Pujar un fitxer" />

    <ion-content class="upload-content">
      <div class="upload-container">
        <FilePond
            name="file"
            allow-multiple="true"
            :server="filePondServerConfig"
            :onprocessfile="onFileProcess"
        />
      </div>
      <Footer />
    </ion-content>
  </ion-page>
</template>

<script setup>
import vueFilePond from 'vue-filepond';
import 'filepond/dist/filepond.min.css';
import FilePondPluginImagePreview from 'filepond-plugin-image-preview';
import 'filepond-plugin-image-preview/dist/filepond-plugin-image-preview.min.css';

import { ref, onMounted } from 'vue';
import Navbar from '../components/Navbar.vue';
import Footer from '../components/Footer.vue';

const FilePond = vueFilePond(FilePondPluginImagePreview);

const token = ref(null);
const filePondServerConfig = ref({});

onMounted(() => {
  token.value = localStorage.getItem('token');

  if (token.value) {
    filePondServerConfig.value = {
      process: {
        url: 'http://127.0.0.1:8000/api/multimedia',
        method: 'POST',
        headers: {
          'Authorization': `Bearer ${token.value}`,
        }
      },
      revert: 'http://127.0.0.1:8000/api/multimedia/revert',
      fetch: 'http://127.0.0.1:8000/api/multimedia/fetch'
    };
  } else {
    console.error('No s\'ha trobat el token. Assegura\'t que estiguis autenticat.');
  }
});

const onFileProcess = (error, file) => {
  if (error) {
    console.error('Error en el procés del fitxer:', error);
  } else {
    console.log('Fitxer processat:', file);
  }
};
</script>

<style scoped>
.upload-content {
  --background: linear-gradient(135deg, #e0f7fa, #80deea);
  padding: 20px;
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
}

.upload-container {
  margin-top: 30px;
  width: 100%;
  max-width: 800px;
  margin-left: auto;
  margin-right: auto;
  background: white;
  border-radius: 16px;
  padding: 30px;
  box-shadow: 0px 8px 20px rgba(0, 0, 0, 0.1);
  transition: transform 0.3s ease, box-shadow 0.3s ease;
}

.upload-container:hover {
  transform: scale(1.02);
  box-shadow: 0px 10px 25px rgba(0, 0, 0, 0.15);
}

.filepond--wrapper {
  margin-top: 20px;
}

@media (max-width: 767px) {
  .upload-container {
    padding: 20px;
  }
}
</style>
