<template>
  <ion-header>
    <ion-toolbar>
      <ion-buttons slot="start">
        <ion-button class="modal-cancel" @click="dismissModal">Cancel</ion-button>
      </ion-buttons>
      <ion-buttons slot=end>
        <ion-button class="modal-confirm" @click="createTask">Create</ion-button>
      </ion-buttons>
    </ion-toolbar>
  </ion-header>
  <ion-content class="ion-padding">
    <form autocomplete="off">
      <div class="form-row">
        <div class="form-column">
          <ion-item>
            <ion-label position="floating">Task Title</ion-label>
            <ion-input autocomplete="off" v-model="task.title"></ion-input>
          </ion-item>
        </div>
      </div>
    </form>
  </ion-content>
</template>

<script>
import {
  IonContent,
  IonHeader,
  IonToolbar,
  modalController,
  IonLabel,
  IonInput,
  IonItem,
  IonButtons,
  IonButton
} from '@ionic/vue';
import {defineComponent, ref} from 'vue';

export default defineComponent({
  name: 'TaskModal',
  components: {
    IonContent,
    IonHeader,
    IonToolbar,
    IonLabel,
    IonInput,
    IonItem,
    IonButtons,
    IonButton
  },
  setup() {
    const role = ref('cancel');
    const task = {
      id: '',
      title: '',
    };

    const dismissModal = async () => {
      await modalController.dismiss(task, role.value);
    }

    const createTask = () => {
      role.value = 'create';
      task.id = String(Date.now());
      dismissModal();
    }

    return {
      task,
      role,
      dismissModal,
      createTask
    }
  }
});
</script>

<style scoped>
ion-title {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  text-align: center;
}

.form-row {
  position: relative;
  width: 100%;
  display: flex;
  flex-direction: row;
  flex-wrap: wrap;
  flex: auto;
  justify-content: space-between;
}

.form-column {
  position: relative;
  display: flex;
  flex-direction: column;
  box-sizing: border-box;
  flex: 100;
  padding: 5px;
}

.modal-confirm {
  --color: var(--ion-color-primary);
  font-weight: 600;
}

.modal-cancel {
  --color: var(--ion-text-color);
  font-weight: 500;
}

ion-item.ios::part(native) {
  padding-inline-start: 0;
}

@media (max-width: 300px) {

  .form-column.column-half {
    min-width: 100%;
  }
}
</style>