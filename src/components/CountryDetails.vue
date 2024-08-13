<template>
  <div class="details">
    <h2>{{ country.name }}</h2>
    <img :src="countryImageUrl" alt="Country Image" v-if="country.flag" />
    <p>Rank: {{ country.rank }}</p>
    <p>Continent: {{ country.continent }}</p>
  </div>
</template>

<script>
export default {
  props: ['country'],
  computed: {
    countryImageUrl() {
      return this.country && this.country.flag 
        ? `http://localhost:8080/${this.country.flag}`
        : '';
    }
  }
};
</script>

<style scoped>
.details {
  text-align: left;
}

.details img {
  max-width: 100%;
  width: 150px;
  height: auto;
  border-radius: 5px;
}
</style>
