<script>
export default {
  mounted() {
    const bgKey = localStorage.getItem("bg-link");
    if (bgKey) {
      document.body.style.backgroundImage = `url(${bgKey})`;
    }
  },
  data() {
    return {
      bgInput: ''
    }
  },
  methods: {
    handleBGChange() {
      if (this.bgInput.trim() !== "") {
        document.body.style.backgroundImage = `url(${this.bgInput})`;
        localStorage.setItem("bg-link", this.bgInput);
      }
    }
  }
}
</script>

<template>
  <div class="BG">
    <input type="text" v-model="bgInput" placeholder="enter url for bg-image" @keyup.enter="handleBGChange" />
    <button @click="handleBGChange">change BG</button>
  </div>
</template>

<style lang="css" scoped>
body {
  background-size: cover;
  background-position: center;
}

.BG {
  margin: 5px 5px 0 5px;
  width: 100%;
  display: flex;
  flex-wrap: nowrap;
}

.BG input,
.BG button {
  font-size: 1rem;
  font-family: monospace;
  padding: 0.5rem 0.8rem;
  border-width: 2px;
  border-style: inset;
  border-color: light-dark(rgb(118, 118, 118), rgb(133, 133, 133));
  border-image: initial;
  border-radius: 0.5rem;
  margin: 0;
  white-space: nowrap;
}

.BG input {
  width: 100%;
  max-width: 25rem;
  min-width: 1rem;
}

.BG button {
  margin: 0 0 0 1rem;
  cursor: pointer;
}

@media (max-width: 365px) {

  .BG input,
  .BG button {
    font-size: 0.8rem;
    padding: 0.4rem 0.4rem;
  }
}
</style>
