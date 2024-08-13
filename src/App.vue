<template>
  <div id="app">
    <header>
      <h1>Country Management</h1>
    </header>
    <main>
      <CountryDropdown 
        :countries="countries" 
        v-model="selectedCountryId" 
        @dropChange="fetchCountryDetails"
        class="dropdown" 
      />
      <CountryDetails :country="selectedCountry" v-if="selectedCountry" class="details" />
      <AddCountryForm @handleSubmit="handleAddCountry" ref="addCountryForm" :uniqueContinents="getUniqueContinents()" class="add-country-form" />
    </main>
  </div>
</template>

<script>
import axios from 'axios';
import CountryDropdown from './components/CountryDropdown.vue';
import CountryDetails from './components/CountryDetails.vue';
import AddCountryForm from './components/AddCountryForm.vue';

export default {
  components: {
    CountryDropdown,
    CountryDetails,
    AddCountryForm
  },
  data() {
    return {
      countries: [],
      selectedCountryId: '',
      selectedCountry: null
    };
  },
  methods: {
    getUniqueContinents() {
      const continents = this.countries.map(c => c.continent);
      return [...new Set(continents)];
    },
    fetchCountries() {
      axios.get('http://localhost:8080/countries')
        .then(response => {
          this.countries = response.data;
        })
        .catch(error => {
          console.error(error);
        });
    },
    fetchCountryDetails(countryId) {
      console.log(countryId)
      axios.get(`http://localhost:8080/country/${countryId}`)
        .then(response => {
          this.selectedCountry = response.data;
        })
        .catch(error => {
          console.error(error);
        });
    },
    handleAddCountry(formData) {
      axios.post('http://localhost:8080/country', formData, {
        headers: {
          'Content-Type': 'multipart/form-data'
        }
      })
        .then(response => {
          this.countries.push(response.data);
          this.selectedCountryId = response.data.id;
          this.fetchCountryDetails(this.selectedCountryId);
          this.$refs.addCountryForm.resetForm();
        })
        .catch(error => {
          console.log(error)
          if (error.response && error.response.data.error) {
            const errors = {};
            if (error.response.data.error.includes('Name')) {
              errors.name = error.response.data.error;
            }
            if (error.response.data.error.includes('Rank')) {
              errors.rank = error.response.data.error;
            }
            if (this.$refs.addCountryForm) {
              this.$refs.addCountryForm.highlightErrors(errors);
            } else {
              console.error('AddCountryForm ref is not available');
            }
          }
        });
    }
  },
  mounted() {
    this.fetchCountries();
  }
};
</script>

<style scoped>
#app {
  font-family: Arial, sans-serif;
  text-align: center;
  color: #333;
  background-color: #f4f4f4;
  padding: 20px;
}

header {
  background-color: #007bff;
  color: #fff;
  padding: 10px;
  border-radius: 5px;
}

h1 {
  margin: 0;
}

main {
  margin-top: 20px;
}

.dropdown, .details, .add-country-form {
  margin: 20px auto;
  padding: 15px;
  max-width: 600px;
  border-radius: 5px;
  background: #fff;
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
}

.dropdown select, .add-country-form input, .add-country-form button {
  margin-top: 10px;
  padding: 10px;
  width: 100%;
  border-radius: 5px;
  border: 1px solid #ddd;
}

.details img {
  max-width: 100%;
  height: auto;
  border-radius: 5px;
}

.add-country-form input, .add-country-form button {
  border: 1px solid #ddd;
}

.add-country-form button {
  background-color: #007bff;
  color: #fff;
  border: none;
  cursor: pointer;
}

.add-country-form button:hover {
  background-color: #0056b3;
}

.error {
  border-color: red;
}
</style>