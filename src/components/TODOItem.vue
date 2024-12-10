<script>
import { createApp } from 'vue';

export default {
  props: {
    index: Number,
    value: {
      title: String,
      date: String,
      img: String
    },
    removeItem: Function
  },
  methods: {
    async openParticleImageOverlay() {
      const { default: ParticleImage } = await import('./ParticleImage.vue');

      const cont = document.createElement("div");
      document.body.appendChild(cont);

      const app = createApp(ParticleImage, {
        imageURL: this.value.img,
        onClose: () => {
          app.unmount();
          document.body.removeChild(cont);
        }
      })

      app.mount(cont);
    }
  }
}
</script>

<script setup>
import { PxDelete } from '@kalimahapps/vue-icons';
</script>

<template>
  <div class="TODOItem">

    <div class="cont">

      <div class="top">
        <div>{{ index }}</div>
        <div>{{ value.date }}</div>
      </div>

      <div>
        <h3>{{ value.title }}</h3>
      </div>

      <div v-if="value.img" class="img-cont" @click="openParticleImageOverlay">
        <img :src="value.img" alt="">
      </div>

      <div class="actions">
        <PxDelete @click="$emit('removeItem', index)" />
      </div>

    </div>

  </div>
</template>

<style lang="css" scoped>
.TODOItem {
  background-color: rgba(150, 150, 150, 0.3);
  border: 3px dotted;
  display: flex;
  flex-direction: column;
  width: 100%;
  box-sizing: border-box;
}

.TODOItem .cont {
  max-width: 60rem;
  width: 100%;
  margin: 0 auto;
}

.TODOItem .top {
  display: flex;
  flex-direction: row;
  justify-content: space-between;
}

.TODOItem .actions {
  display: flex;
  justify-content: flex-end;
  font-size: 1.4rem;
}

.TODOItem .actions svg {
  cursor: pointer;
}

.TODOItem h3 {
  margin: 0;
  padding: 0;
  font-size: 3rem;
}

.TODOItem .img-cont {
  width: 100%;
  max-width: 35rem;
  height: auto;
  cursor: pointer;
}

.TODOItem .img-cont img {
  width: 100%;
  height: 100%;
}
</style>
