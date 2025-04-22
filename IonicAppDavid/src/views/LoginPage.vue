<template>
  <ion-page>
    <Navbar title="Benvingut!" />
    <ion-content :fullscreen="true" class="auth-content">
      <div class="auth-container">
        <h1 class="auth-title">{{ isRegister ? 'Crea el teu compte' : 'Inicia sessió' }}</h1>
        <p class="auth-subtitle">
          {{ isRegister ? 'Uneix-te a nosaltres avui mateix!' : 'Accedeix al teu compte per continuar.' }}
        </p>

        <ion-card class="auth-card">
          <ion-card-content class="card-content">
            <ion-item class="form-item" v-if="isRegister">
              <ion-label position="floating">Nom</ion-label>
              <ion-input v-model="form.name" placeholder="Introdueix el teu nom complet" />
            </ion-item>

            <ion-item class="form-item">
              <ion-label position="floating">Correu electrònic</ion-label>
              <ion-input v-model="form.email" type="email" placeholder="Introdueix el teu correu electrònic" />
            </ion-item>

            <ion-item class="form-item">
              <ion-label position="floating">Contrasenya</ion-label>
              <ion-input v-model="form.password" type="password" placeholder="Tria una contrasenya segura" />
            </ion-item>

            <ion-item class="form-item" v-if="isRegister">
              <ion-label position="floating">Confirmar contrasenya</ion-label>
              <ion-input v-model="form.confirmPassword" type="password" placeholder="Confirma la teva contrasenya" />
            </ion-item>

            <ion-button expand="block" @click="submit" class="btn-primary">
              {{ isRegister ? 'Registrar-se' : 'Iniciar sessió' }}
            </ion-button>

            <ion-button expand="block" fill="clear" @click="isRegister = !isRegister" class="btn-secondary">
              {{ isRegister ? 'Ja tens un compte? Inicia sessió' : 'No tens un compte? Registra\'t' }}
            </ion-button>
          </ion-card-content>
        </ion-card>
      </div>
      <Footer />
    </ion-content>
  </ion-page>
</template>

<script lang="ts">
import { defineComponent, ref, onMounted } from 'vue';
import { IonPage, IonContent, IonCard, IonCardContent, IonItem, IonLabel, IonInput, IonButton } from '@ionic/vue';
import api from '../services/api';
import { useRouter } from 'vue-router';
import Navbar from "@/components/Navbar.vue";
import Footer from "@/components/Footer.vue";

export default defineComponent({
  name: 'LoginPage',
  components: {
    Footer,
    Navbar,
    IonPage,
    IonContent,
    IonCard,
    IonCardContent,
    IonItem,
    IonLabel,
    IonInput,
    IonButton
  },
  setup() {
    const router = useRouter();
    const isRegister = ref(false);
    const isLoggedIn = ref(!!localStorage.getItem('token'));
    const form = ref({ name: '', email: '', password: '', confirmPassword: '' });

    onMounted(() => {
      if (isLoggedIn.value) {
        router.push('/user');
      }
    });

    const submit = async () => {
      try {
        if (isRegister.value) {
          if (!form.value.name.trim()) {
            alert('Si us plau, introdueix un nom.');
            return;
          }
          if (!form.value.confirmPassword || form.value.password !== form.value.confirmPassword) {
            alert('Les contrasenyes no coincideixen.');
            return;
          }
        }
        if (!form.value.email.trim() || !form.value.password.trim()) {
          alert('Si us plau, introdueix tant el correu electrònic com la contrasenya.');
          return;
        }

        const endpoint = isRegister.value ? '/register' : '/login';
        const payload = {
          email: form.value.email,
          password: form.value.password,
          device_name: 'web',
          ...(isRegister.value && { name: form.value.name })
        };

        const response = await api.post(endpoint, payload);
        localStorage.setItem('token', response.data.token);
        isLoggedIn.value = true;
        router.push('/home');
      } catch (error) {
        console.error('Error d\'autenticació:', error.response?.data || error.message);
        alert('Error d\'autenticació: ' + (error.response?.data?.message || 'Error desconegut'));
      }
    };

    return { isRegister, isLoggedIn, form, submit };
  }
});
</script>

<style scoped>
.auth-content {
  --background: linear-gradient(135deg, #6366f1, #4f46e5);
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 2rem;
  min-height: 100vh;
}

.auth-container {
  text-align: center;
  color: white;
}

.auth-title {
  font-size: 2rem;
  font-weight: bold;
  margin-bottom: 0.5rem;
}

.auth-subtitle {
  font-size: 1rem;
  margin-bottom: 2rem;
  opacity: 0.9;
}

.auth-card {
  background: white;
  border-radius: 20px;
  padding: 2rem;
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.1);
  width: 100%;
  max-width: 420px;
  margin: 0 auto;
}

.card-content {
  display: flex;
  flex-direction: column;
  gap: 1.25rem;
}

.btn-primary {
  --background: #4f46e5;
  --color: white;
  font-weight: 600;
  font-size: 1rem;
  border-radius: 12px;
  padding: 0.75rem;
  box-shadow: 0 4px 14px rgba(79, 70, 229, 0.4);
  transition: background 0.3s ease;
}

.btn-primary:hover {
  --background: #3b3abf;
}

.btn-secondary {
  --color: #4f46e5;
  font-weight: 500;
  font-size: 0.95rem;
  text-align: center;
  margin-top: 0.5rem;
}

.form-item {
  margin-bottom: 1.5rem; /* Augmenta l'espai entre els camps */
}

ion-label {
  padding-top: 15px;
  margin-bottom: 15px; /* Espai entre el label i l'input */
  display: block; /* Assegura que el label estigui separat de l'input */
}
</style>