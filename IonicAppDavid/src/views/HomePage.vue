<template>
  <ion-page>
    <Navbar title="Llista d'Arxius" />

    <ion-content class="media-content">
      <!-- Botó flotant per pujar un fitxer -->
      <ion-button expand="full" class="btn-upload" @click="goToUploadPage">
        <img src="@/assets/add.png" alt="Pujar un fitxer" class="btn-upload-icon" />
      </ion-button>

      <!-- Llista d'arxius multimèdia en mode grid -->
      <ion-grid class="media-grid">
        <ion-row>
          <ion-col
              size="6"
              size-sm="4"
              size-md="3"
              v-for="(item, index) in multimedia"
              :key="index"
          >
            <ion-card class="media-card">
              <ion-card-header class="media-header">
                <img :src="getPreviewSrc(item)" alt="Previsualització" />
              </ion-card-header>

              <ion-card-footer class="media-footer">
                <ion-item lines="none" class="media-user">
                  <ion-avatar slot="start">
                    <img :src="item.user.profile_photo_url" />
                  </ion-avatar>
                  <ion-label class="media-username">
                    {{ item.user.name }}
                  </ion-label>
                </ion-item>

                <ion-item lines="none" class="media-date">
                  <ion-label>
                    <p>{{ formatDate(item.created_at) }} - Fa {{ getTimeAgo(item.created_at) }}</p>
                  </ion-label>
                </ion-item>
              </ion-card-footer>
            </ion-card>
          </ion-col>
        </ion-row>
      </ion-grid>

      <Footer />
    </ion-content>
  </ion-page>
</template>

<script setup>
import Navbar from '../components/Navbar.vue';
import Footer from '../components/Footer.vue';
import { ref, onMounted } from 'vue';
import { useRouter } from 'vue-router';
import api from '../services/api';

const router = useRouter();
const multimedia = ref([]);

const getMultimedia = async () => {
  try {
    const response = await api.get('/multimedia');
    multimedia.value = response.data;
  } catch (error) {
    console.error('Error getting multimedia:', error);
  }
};

const goToUploadPage = () => {
  router.push('/upload').then(() => {
    window.location.reload();
  });
};

import videoIcon from '@/assets/video.png';
import documentIcon from '@/assets/docs.png';
const getPreviewSrc = (item) => {
  if (item.file_type.startsWith('image/')) {
    return `data:image/jpeg;base64,${item.file_path}`;
  } else if (item.file_type.startsWith('video/')) {
    return videoIcon;
  } else {
    return documentIcon;
  }
};

const formatDate = (dateString) => {
  const date = new Date(dateString);
  return date.toLocaleString();
};

const getTimeAgo = (dateString) => {
  const now = new Date();
  const diffInSeconds = Math.floor((now - new Date(dateString)) / 1000);

  const minutes = Math.floor(diffInSeconds / 60);
  const hours = Math.floor(diffInSeconds / 3600);
  const days = Math.floor(diffInSeconds / 86400);

  if (hours < 1) return `${minutes} minut${minutes === 1 ? '' : 's'}`;
  if (hours < 24) return `${hours} hora${hours === 1 ? '' : 's'}`;
  return `${days} dia${days === 1 ? '' : 's'}`;
};

onMounted(getMultimedia);
</script>

<style scoped>
.media-content {
  --background: linear-gradient(135deg, #e0eafc, #cfdef3);
  padding-bottom: 60px;
}

.btn-upload {
  position: fixed;
  bottom: 70px;
  right: 20px;
  background-color: #4caf50;
  border-radius: 50%;
  height: 60px;
  width: 60px;
  display: flex;
  justify-content: center;
  align-items: center;
  box-shadow: 0px 6px 12px rgba(0, 0, 0, 0.2);
  z-index: 10;
  transition: transform 0.3s ease, background-color 0.3s ease;
}

.btn-upload:hover {
  background-color: #388e3c;
  transform: scale(1.1);
}

.btn-upload-icon {
  width: 28px;
  height: 28px;
}

.media-grid {
  padding: 20px;
  gap: 20px;
}

.media-card {
  border-radius: 16px;
  box-shadow: 0px 6px 15px rgba(0, 0, 0, 0.1);
  margin-bottom: 20px;
  background-color: #ffffff;
  transition: transform 0.3s ease, box-shadow 0.3s ease;
}

.media-card:hover {
  transform: scale(1.05);
  box-shadow: 0px 8px 20px rgba(0, 0, 0, 0.15);
}

.media-header {
  background-color: #f0f4f8;
  padding: 15px;
  border-top-left-radius: 16px;
  border-top-right-radius: 16px;
  text-align: center;
}

.media-header img {
  max-width: 100%;
  border-radius: 12px;
}

.media-footer {
  padding: 15px;
}

.media-user,
.media-date {
  display: flex;
  align-items: center;
  gap: 10px;
}

.media-username {
  font-size: 15px;
  font-weight: 600;
  color: #333;
}

.media-date p {
  margin: 0;
  font-size: 13px;
  color: #777;
}

ion-avatar img {
  border-radius: 50%;
  width: 45px;
  height: 45px;
  box-shadow: 0px 2px 6px rgba(0, 0, 0, 0.1);
}

@media (max-width: 767px) {
  ion-col {
    padding: 10px;
  }
}
</style>
