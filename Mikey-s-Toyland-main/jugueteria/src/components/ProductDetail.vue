<template>
  <div class="product-detail" v-if="product">
    <button @click="$router.push('/')">Volver</button>
    <div class="image">
      <img :src="'http://127.0.0.1:3000/images/' + product.image" alt="">
    </div>
    <div class="info">
      <h1>{{ product.name }}</h1>
      <p style="font-weight: bold; font-size: 25px;"><strong></strong> {{ product.price }}$</p>
      <br>
      <br>
      <p><strong>Descripción:</strong> {{ product.description }}</p>
      <p><strong>Empresa:</strong> {{ product.company }}</p>
      <p><strong>Tamaño:</strong> {{ product.size }}</p>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  props: ['id'],
  data() {
    return {
      product: null,
    };
  },
  mounted() {
    axios
      .get(`http://127.0.0.1:3000/toys/${this.id}`)
      .then((response) => {
        this.product = response.data.data;
      })
      .catch((error) => {
        console.error(error);
      });
  },
};
</script>

<style scoped>
.product-detail {
  display: flex;
  align-items: flex-start; 
  gap: 20px; /* Espacio entre imagen e información */
  border-radius: 20px;
  background-color: #123d46;
  padding: 20px;
  color: #ffffff;
}

.image img {
  width: 400px;
  height: auto;
  border-radius: 20px;
  
}

.info {
  max-width: 600px; 
  text-align: left;
}

h1 {
  font-size: 28px;
  margin-bottom: 10px;
}

p {
  font-size: 18px;
  margin-bottom: 10px;
}

button {
  padding: 10px 20px;
  background-color: #01c38d;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  position: absolute;
  left: 2%;
  top: 2%;
}
</style>
