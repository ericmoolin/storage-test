<template>
  <ion-page>
    <ion-header>
      <ion-toolbar>
        <ion-title>Ionic Storage Test</ion-title>
      </ion-toolbar>
    </ion-header>
    <ion-content :fullscreen="true">
      <ion-list>
        <ion-item v-for="task in tasks" :key="task.id">
          <ion-label>{{ task.title }}</ion-label>
          <ion-checkbox slot="start"></ion-checkbox>
        </ion-item>
      </ion-list>
      <ion-fab vertical="bottom" horizontal="end" slot="fixed">
        <ion-fab-button @click="taskModal">
          <ion-icon :icon="addCircle"></ion-icon>
        </ion-fab-button>
      </ion-fab>
    </ion-content>
  </ion-page>
</template>

<script>
import {IonContent, IonHeader, IonPage, IonTitle, IonToolbar, modalController, IonIcon, IonFab, IonFabButton, IonCheckbox, IonItem, IonList, IonLabel} from '@ionic/vue';
import {ref, defineComponent} from 'vue';
import {addCircle, caretForwardCircle} from 'ionicons/icons';
import TaskModal from "@/components/TaskModal";
import {Drivers, Storage} from '@ionic/storage';
import * as CordovaSQLiteDriver from 'localforage-cordovasqlitedriver';

export default defineComponent({
  name: 'Home',
  components: {
    IonContent,
    IonHeader,
    IonPage,
    IonTitle,
    IonToolbar,
    IonIcon,
    IonFab,
    IonFabButton,
    IonCheckbox,
    IonItem,
    IonList,
    IonLabel
  },
  setup() {
    const tasks = ref([]);
    const tasksDB = new Storage({
      name: 'tasks',
      storeName: 'tasks',
      driverOrder: [
        CordovaSQLiteDriver._driver,
        Drivers.IndexedDB,
        Drivers.LocalStorage
      ]
    });

    //Ionic-storage functions
    const setDatabaseEntry = async (database, key, value) => {
      await database.set(key, value);
      tasks.value = getDatabaseValues(tasksDB);
    }

    const getDatabaseValues = (database) => {
      let values = [];
      database.forEach((value) => {
        values.push(value);
      });
      return values;
    }

    const initializeDatabase = async (database) => {
      await database.defineDriver(CordovaSQLiteDriver);
      await database.create();
      console.log(String(database._config.name) + ' database initialized');
      tasks.value = getDatabaseValues(tasksDB);
    }

    //Popup modal for list item creation
    const taskModal = async () => {
      const modal = await modalController
          .create({
            component: TaskModal,
            cssClass: 'task-modal'
          });
      modal.onDidDismiss()
          .then((data) => {
            if (data.role === 'create') {
              let task = Object.assign({}, data.data);
              setDatabaseEntry(tasksDB, task.id, task);
            }
          });
      return modal.present();
    }

    initializeDatabase(tasksDB);

    return {
      addCircle,
      caretForwardCircle,
      tasks,
      tasksDB,
      taskModal
    };
  }
})
;
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
</style>