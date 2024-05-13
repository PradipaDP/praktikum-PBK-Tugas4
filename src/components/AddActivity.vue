<!-- AddActivity.vue -->
<template>
  <div>
    <input v-model="newActivity" @keyup.enter="addActivity" placeholder="Add new activity" />
    <button @click="addActivity">Add</button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      newActivity: ''
    };
  },
  methods: {
    addActivity() {
      if (this.newActivity.trim() !== '') {
        this.$emit('add', this.newActivity);
        console.log(`Adding activity: ${this.newActivity}`);
        this.newActivity = '';
      }
    }
  }
}
</script>

<style scoped>
input, button {
  padding: 10px;
  margin-right: 10px;
  border: none;
  border-radius: 4px;
}

button {
  background-color: #ee6c4d;
  color: white;
  cursor: pointer;
  padding-top: 6px;
}

button:hover {
  background-color: #0056b3;
}

</style>
