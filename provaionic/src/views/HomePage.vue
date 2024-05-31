```html
<template>
  <!-- Pàgina principal de l'aplicació -->
  <ion-page>
    <!-- Capçalera de la pàgina -->
    <ion-header :translucent="true">
      <ion-toolbar>
        <!-- Títol de la pàgina -->
        <ion-title>Time Fighter</ion-title>
        <ion-buttons slot="primary">
          <!-- Botó d'informació que mostra una alerta al clicar -->
          <ion-button color="primary" fill="solid" @click="presentInfoAlert">
            <ion-icon :icon="infoIcon"></ion-icon>
          </ion-button>
        </ion-buttons>
      </ion-toolbar>
    </ion-header>

    <!-- Contingut principal de la pàgina -->
    <ion-content :fullscreen="true">
      <ion-header class="ion-no-border ion-padding-top ion-padding-horizontal">
        <ion-grid>
          <ion-row>
            <ion-col>
              <!-- Puntuació actual de l'usuari -->
              <div class="ion-text-start">
                Puntuació: {{ score }}
              </div>
            </ion-col>
            <ion-col>
              <!-- Temps restant -->
              <div class="ion-text-end">
                Temps restant: {{ timeLeft }}
              </div>
            </ion-col>
          </ion-row>
        </ion-grid>
      </ion-header>

      <!-- Contenidor principal de la pàgina -->
      <div id="container">
        <!-- Canvas on es mostra el confeti -->
        <canvas id="confetti-canvas"></canvas>
        <div id="container">
          <!-- Botó "Tap me" que incrementa la puntuació al clicar -->
          <ion-button color="primary" @click="tap">
            Tap me
          </ion-button>
        </div>
      </div>
    </ion-content>
  </ion-page>
</template>

<script setup lang="ts">
// Importa els components necessaris de Ionic i Vue
import {
  IonButton,
  IonContent,
  IonHeader,
  IonPage,
  IonTitle,
  IonToolbar,
  IonIcon,
  IonGrid,
  IonRow,
  IonCol,
  alertController,
  toastController,
} from '@ionic/vue';
import { informationCircleOutline } from 'ionicons/icons';
import { ref } from 'vue';
import confetti from 'canvas-confetti';

// Icona d'informació que es mostra al botó d'informació
const infoIcon = informationCircleOutline;

// Puntuació actual de l'usuari
const score = ref(0);
// Temps restant en el joc
const timeLeft = ref(10); // Canviat a 10 segons

// Compta enrere del temps
let timer;

// Mostra una alerta quan el temps s'acaba
const presentAlert = async () => {
  const alert = await alertController.create({
    header: 'Alerta de Time Fighter',
    message: 'S\'ha acabat el temps! El teu joc ha finalitzat.',
    buttons: ['D\'acord'],
  });

  await alert.present();
};

// Mostra una alerta d'informació al clicar el botó d'informació
const presentInfoAlert = async () => {
  const infoAlert = await alertController.create({
    header: 'Informació de Time Fighter',
    message: 'Aplicació feta per Joel Iglesias',
    buttons: ['D\'acord'],
  });

  await infoAlert.present();
};

// Mostra el confeti a la pantalla
const showConfetti = () => {
  const canvas = document.getElementById('confetti-canvas');
  if (canvas instanceof HTMLCanvasElement) {
    confetti.create(canvas, {
      resize: true,
      useWorker: true,
    })({
      particleCount: 200,
      spread: 360,
      origin: { x: 0.5, y: 0.5 },
    });
  } else {
    // Gestionar l'error aquí, per exemple, mostrar un missatge d'error a l'usuari
  }
};

// Reinicia el joc
const restartGame = () => {
  // Restablir la puntuació i el temps
  score.value = 0;
  timeLeft.value = 10;

  // Detén qualsevol compte enrere activa
  clearInterval(timer);
};

// Incrementa la puntuació quan l'usuari fa clic al botó "Tap me"
const tap = async () => {
  try {
    // Reinicia el joc si el temps ha arribat a zero
    if (timeLeft.value <= 0) {
      restartGame();
    }

    // Incrementa la puntuació en un amb cada clic
    score.value += 1;

    // Mostra el confeti
    showConfetti();

    // Si és el primer clic, inicia la compte enrere del temps
    if (score.value === 1) {
      countdown();
    }

    // Mostra el toast
    const toast = await toastController.create({
      message: 'Has clicat el botó!',
      duration: 2000,
    });

    await toast.present();
  } catch (error) {
    console.error('Error en fer clic al botó:', error);
  }
};

// Inicia la compte enrere quan l'usuari fa el primer clic
const countdown = () => {
  timer = setInterval(() => {
    timeLeft.value -= 1;

    if (timeLeft.value <= 0) {
      clearInterval(timer);
      // Mostra l'alerta quan el temps arriba a zero
      presentAlert();
    }
  }, 1000);
};
</script>

<style scoped>
#confetti-canvas,
#container {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  z-index: 1;
}

ion-button {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  z-index: 2;
}

#container strong {
  font-size: 20px;
  line-height: 26px;
}

#container p {
  font-size: 16px;
  line-height: 22px;
  color: #8c8c8c;
  margin: 0;
}

#container a {
  text-decoration: none;
}
</style>
