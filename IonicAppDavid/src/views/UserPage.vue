<template>
  <ion-page>
    <Navbar title="Perfil d'Usuari"/>

    <ion-content class="profile-content">
      <ion-button expand="full" class="logout-button" @click="logout">
        Tancar Sessió
      </ion-button>

      <ion-list class="file-list">
        <ion-item v-for="(item, index) in userFiles" :key="index" class="file-item">
          <div class="file-info">
            <img :src="getPreviewSrc(item)" alt="Previsualització" class="file-preview" />
            <div class="file-details">
              <p class="file-date">Creat: {{ formatDate(item.created_at) }}</p>
              <p class="file-date">Modificat: {{ formatDate(item.updated_at) }}</p>
            </div>
          </div>
          <ion-button fill="clear" class="delete-button" @click="deleteFile(item.id)">
            Eliminar
          </ion-button>
        </ion-item>
      </ion-list>

      <Footer />
    </ion-content>
  </ion-page>
</template>

<script setup>
import {ref, onMounted} from 'vue';
import {useRouter} from 'vue-router';
import api from '../services/api';
import Navbar from '../components/Navbar.vue';
import Footer from '../components/Footer.vue';

const router = useRouter();
const userFiles = ref([]);

const getUserFiles = async () => {
  try {
    const response = await api.get(`/multimedia/user`);
    userFiles.value = response.data;
  } catch (error) {
    console.error('Error obtenint fitxers:', error);
  }
};

const formatDate = (dateString) => {
  const date = new Date(dateString);
  return date.toLocaleString();
};

const logout = async () => {
  try {
    localStorage.removeItem('token');
    router.push('/login').then(() => {
      window.location.reload();
    });
  } catch (error) {
    console.error('Error logging out:', error);
  }
};

const deleteFile = async (fileId) => {
  try {
    await api.delete(`/multimedia/${fileId}`);
    userFiles.value = userFiles.value.filter(file => file.id !== fileId);
  } catch (error) {
    console.error('Error deleting file:', error);
  }
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

onMounted(getUserFiles);
</script>

<style scoped>
.profile-content {
  --background: linear-gradient(135deg, #f0f4f8, #d9e2ec);
  padding: 20px;
  padding-bottom: 80px;
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.logout-button {
  background-color: #ff6b6b;
  color: white;
  font-size: 16px;
  font-weight: bold;
  border-radius: 12px;
  margin-bottom: 30px;
  padding: 12px 20px;
  box-shadow: 0px 4px 10px rgba(0, 0, 0, 0.1);
  transition: background-color 0.3s ease, transform 0.2s ease;
}

.logout-button:hover {
  background-color: #e63946;
  transform: scale(1.05);
}

.file-list {
  width: 100%;
  max-width: 800px;
  display: flex;
  flex-direction: column;
  gap: 20px;
}

.file-item {
  background-color: white;
  border-radius: 16px;
  padding: 20px;
  display: flex;
  justify-content: space-between;
  align-items: center;
  box-shadow: 0px 3px 12px rgba(0, 0, 0, 0.1);
  transition: transform 0.2s ease, box-shadow 0.2s ease;
}

.file-item:hover {
  transform: scale(1.02);
  box-shadow: 0px 5px 15px rgba(0, 0, 0, 0.15);
}

.file-info {
  display: flex;
  align-items: center;
  gap: 20px;
}

.file-preview {
  width: 80px;
  height: 80px;
  object-fit: cover;
  border-radius: 12px;
  box-shadow: 0px 2px 6px rgba(0, 0, 0, 0.1);
}

.file-details {
  display: flex;
  flex-direction: column;
}

.file-date {
  font-size: 14px;
  color: #555;
  margin: 2px 0;
}

.delete-button {
  background-color: #ff6b6b;
  color: white;
  font-weight: bold;
  border-radius: 10px;
  padding: 8px 12px;
  transition: background-color 0.3s ease, transform 0.2s ease;
}

.delete-button:hover {
  background-color: #e63946;
  transform: scale(1.05);
}

@media (max-width: 767px) {
  .file-item {
    flex-direction: column;
    align-items: flex-start;
    padding: 20px;
  }

  .file-info {
    flex-direction: column;
    align-items: flex-start;
    gap: 15px;
    width: 100%;
  }

  .delete-button {
    margin-top: 10px;
    width: 100%;
  }
}
</style>
