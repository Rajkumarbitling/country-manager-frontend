<template>
  <form @submit.prevent="handleSubmit" ref="form">
    <div class="form-control">
      <label for="name">Country Name:</label>
      <input type="text" id="name" v-model="newCountry.name" :class="{ error: errors.name }" />
      <span class="error-message" v-if="errors.name">{{ errors.name }}</span>
    </div>
    <div class="form-control">
      <label for="rank">Rank:</label>
      <input type="number" id="rank" v-model="newCountry.rank" :class="{ error: errors.rank }" />
      <span class="error-message" v-if="errors.rank">{{ errors.rank }}</span>
    </div>
    <div class="form-control">
      <label for="continent">Continent:</label>
      <select id="continent" v-model="newCountry.continent">
        <option v-for="cont in uniqueContinents" :key="cont" :value="cont">{{ cont }}</option>
      </select>
    </div>
    <div class="form-control">
      <label for="image">Country Image:</label>
      <input type="file" id="image" ref="fileInput" @change="handleFileChange" />
    </div>
    <button type="submit">Add Country</button>
  </form>
</template>
  
<script>
export default {
  props: ['uniqueContinents'],
  data() {
    return {
      newCountry: {
        name: '',
        rank: '',
        continent: '',
        image: null
      },
      errors: {}
    };
  },
  methods: {
    handleFileChange(event) {
      this.newCountry.image = event.target.files[0];
    },
    handleSubmit() {
      this.clearErrors();
      
      // Basic form validation
      if (!this.newCountry.name || !this.newCountry.rank || !this.newCountry.continent) {
        this.errors = {
          name: !this.newCountry.name,
          rank: !this.newCountry.rank,
          continent: !this.newCountry.continent
        };
        return;
      }

      const formData = new FormData();
      formData.append('name', this.newCountry.name);
      formData.append('rank', this.newCountry.rank);
      formData.append('continent', this.newCountry.continent);
      if (this.newCountry.image) {
        formData.append('image', this.newCountry.image);
      }

      this.$emit('handleSubmit', formData);
    },
    clearErrors() {
      this.errors = {};
    },
    highlightErrors(errors) {
      this.errors = errors;
    },
    resetForm() {
      this.newCountry.name = '';
      this.newCountry.rank = '';
      this.newCountry.continent = '';
      this.newCountry.image = null;
      this.$refs.fileInput.value = '';
      this.clearErrors();
    }
  }
};
</script>

<style scoped>
  form {
    display: flex;
    flex-direction: column;
    gap: 15px;
  }

  label {
    margin: 0 10px;
    margin-bottom: 5px;
    font-weight: bold;
  }

  input, select {
    padding: 10px;
    border: 1px solid #ddd;
    border-radius: 5px;
    width: -webkit-fill-available;
  }

  button {
    background-color: #007bff;
    color: #fff;
    font-weight: bold;
    padding: 10px;
    border: none;
    border-radius: 5px;
    cursor: pointer;
  }

  button:hover {
    background-color: #0056b3;
  }

  .error {
    border-color: red;
  }

  .form-control {
    width: 100%;
    display: grid;
    align-items: center;
    justify-items: start;
  }

  .error-message {
    color: #ff0000; /* Red color for error message */
    font-size: 12px;
    margin-top: 4px;
  }
</style>