<template>
    <div class="country-details-container">
      <button @click="goBack" class="back-button">Go Back</button>
      <div v-if="loading" class="loading-container">
        <div class="spinner"></div>
      </div>
      <div v-else-if="error" class="error-container">
        An error occurred: {{ error }}. <button @click="fetchDetails" class="retry-button">Retry</button>
      </div>
      <div v-else class="country-card">
        <img :src="details.flag" class="flag-image" alt="Flag of {{ country }}">
        <div class="country-info">
          <h2 class="country-name">{{ country }}</h2>
          <div class="country-details">
            <p><strong>Population:</strong> {{ details.population }}</p>
            <p><strong>Capital:</strong> {{ details.capital }}</p>
            <p><strong>Region:</strong> {{ details.region }}</p>
            <p><strong>Subregion:</strong> {{ details.subregion }}</p>
            <p><strong>Languages:</strong> {{ Array.isArray(details.languages) ? details.languages.join(', ') : '' }}</p>
            <p><strong>Currencies:</strong> {{ Array.isArray(details.currencies) ? details.currencies.join(', ') : '' }}</p>
          </div>
          <div class="contact-details">
            <h3>Contact Details</h3>
            <p><strong>Phone:</strong> {{ contact.phone }}</p>
            <p><strong>Email:</strong> {{ contact.email }}</p>
          </div>
        </div>
      </div>
    </div>
  </template>
  
  <script>
  import axios from 'axios';
  
  export default {
    name: 'CountryDetails',
    props: ['country'],
    data() {
      return {
        loading: true,
        error: null,
        details: null,
        contact: {
          phone: '',
          email: ''
        }
      };
    },
    methods: {
      goBack() {
        this.$router.go(-1);
      },
      async fetchDetails(retries = 3) {
        this.loading = true;
        this.error = null;
        try {
          const response = await axios.get(`https://restcountries.com/v3.1/name/${this.country}`);
          this.details = response.data[0];
        } catch (error) {
          if (retries > 0) {
            return this.fetchDetails(retries - 1);
          }
          this.error = error.message;
        } finally {
          this.loading = false;
        }
      },
    },
    created() {
      this.fetchDetails();
    },
  };
  </script>
  
  <style scoped>
  .country-details-container {
    max-width: 800px;
    margin: auto;
    padding: 2rem;
    background-color: #f5f5f5;
    border-radius: 10px;
    box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
  }
  
  .back-button {
    padding: 0.5rem 1rem;
    margin-bottom: 1rem;
    background-color: #007bff;
    color: #fff;
    border: none;
    border-radius: 5px;
    cursor: pointer;
  }
  
  .loading-container {
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
  }
  
  .spinner {
    border: 4px solid rgba(0, 0, 0, 0.1);
    width: 36px;
    height: 36px;
    border-radius: 50%;
    border-left-color: #007bff;
    animation: spin 1s linear infinite;
  }
  
  .error-container {
    padding: 1rem;
    background-color: #f8d7da;
    color: #721c24;
    border: 1px solid #f5c6cb;
    border-radius: 5px;
  }
  
  .retry-button {
    padding: 0.25rem 0.5rem;
    background-color: #007bff;
    color: #fff;
    border: none;
    border-radius: 5px;
    cursor: pointer;
  }
  
  .country-card {
    display: flex;
    flex-direction: column;
    align-items: center;
    padding: 2rem;
    background-color: #fff;
    border-radius: 10px;
    box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
  }
  
  .flag-image {
    width: 100%;
    height: auto;
    border-radius: 5px;
  }
  
  .country-info {
    text-align: center;
  }
  
  .country-name {
    margin-top: 1rem;
    font-size: 2rem;
    color: #333;
  }
  
  .country-details {
    margin-top: 1rem;
    font-size: 1.2rem;
    color: #333;
  }
  
  .contact-details {
    margin-top: 2rem;
  }
  
  @keyframes spin {
    0% { transform: rotate(0deg); }
    100% { transform: rotate(360deg); }
  }
  </style>
  