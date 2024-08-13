<template>
  <div>
    <label for="country-select">Select a Country:</label>
    <select id="country-select" :value="selectedCountryId" @change="onChange">
      <option v-for="country in countries" :key="country.id" :value="country.id">
        {{ country.name }}
      </option>
    </select>
  </div>
</template>

<script>
export default {
  props: ['countries', 'selectedCountryId'],
  methods: {
    onChange(e) {
      this.$emit('dropChange', e.target.value);
    }
  }
};
</script>

<style scoped>
.dropdown label {
    margin: 0 10px;
    font-weight: bold;
}
.dropdown select {
  padding: 10px;
  border: 1px solid #ddd;
  border-radius: 5px;
  font-size: 16px;
  width: 100%;
  max-width: 300px;
}
</style>