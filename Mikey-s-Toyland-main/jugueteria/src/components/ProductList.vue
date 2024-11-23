<template>
  <div class="product-target">
    <h1>Juguetrones</h1>
    <div class="products-grid">
      <div class="product-card" v-for="product in paginatedProducts" :key="product.id">
        <router-link :to="'/product/' + product.id">
          <img :src="'http://127.0.0.1:3000/images/' + product.image" alt="">
        </router-link>
        <h2><router-link :to="'/product/' + product.id">{{ product.name }}</router-link></h2>
        <p class="price">{{ product.price }} $</p>
        <div class="buttons">
          <router-link :to="'/productEdit/' + product.id">
            <button>Editar</button>
          </router-link>
          <button @click="showModal(product)">Eliminar</button>
        </div>
      </div>
    </div>
    
    <div class="pagination-buttons">
      <button @click="prevPage" :disabled="currentPage === 1">Anterior</button>
      <button @click="nextPage" :disabled="!hasMore">Siguiente</button>
    </div>
    
    <div class="add-product">
      <button @click="goToAddProduct">Agregar Productos</button>
    </div>
    
    <ConfirmModal
      :visible="isModalVisible"
      @confirm="deleteProduct"
      @cancel="hideModal"
    />
  </div>
</template>

<script>
import axios from 'axios';
import ConfirmModal from './DeleteConfirmation.vue';

export default {
  components: { ConfirmModal },
  data() {
    return {
      products: [],
      currentPage: 1,
      itemsPerPage: 10, // Cambia este valor si quieres mostrar más o menos productos por página
      isModalVisible: false,
      productToDelete: null,
    };
  },
  computed: {
    paginatedProducts() {
      const start = (this.currentPage - 1) * this.itemsPerPage;
      const end = start + this.itemsPerPage;
      return this.products.slice(start, end);
    },
    hasMore() {
      return this.currentPage * this.itemsPerPage < this.products.length;
    },
  },
  mounted() {
    axios
      .get('http://127.0.0.1:3000/toys')
      .then((response) => {
        this.products = response.data.data;
      })
      .catch((error) => {
        console.error(error);
      });
  },
  methods: {
    showModal(product) {
      this.productToDelete = product;
      this.isModalVisible = true;
    },
    hideModal() {
      this.isModalVisible = false;
      this.productToDelete = null;
    },
    deleteProduct() {
      axios
        .delete(`http://127.0.0.1:3000/toys/${this.productToDelete.id}`)
        .then(() => {
          this.products = this.products.filter(
            (p) => p.id !== this.productToDelete.id
          );
          this.hideModal();
          // Si eliminamos el último producto en una página, regresamos a la anterior si es necesario
          if (this.paginatedProducts.length === 0 && this.currentPage > 1) {
            this.currentPage--;
          }
        })
        .catch((error) => {
          console.error(error);
        });
    },
    prevPage() {
      if (this.currentPage > 1) {
        this.currentPage -= 1;
      }
    },
    nextPage() {
      if (this.hasMore) {
        this.currentPage += 1;
      }
    },
    goToAddProduct() {
      this.$router.push('/productform');
    },
  },
};
</script>

<style scoped>
.product-target {
  display: flex;
  flex-direction: column;
  align-items: center;
  text-align: center;
}

.products-grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr); /* Tres columnas */
  gap: 20px;
  margin: 20px 0;
  width: 100%;
}

.product-card {
  background-color: #ffffff;
  padding: 15px;
  border-radius: 10px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  text-align: center;
}

.product-card img {
  width: 100%;
  height: auto;
  border-radius: 10px;
}

.product-card h2 {
  font-size: 20px;
  margin: 10px 0;
}

.price {
  font-weight: bold;
  color: #000000;
  font-size: 18px;
}

.buttons {
  display: flex;
  justify-content: center;
  gap: 10px;
  margin-top: 10px;
}

.pagination-buttons {
  margin: 20px;
  display: flex;
  gap: 10px;
}

button {
  padding: 5px 10px;
  border: none;
  background-color: #132d46;
  color: white;
  border-radius: 5px;
  cursor: pointer;
}

button:hover {
  background-color: #01c38d;
}

button:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}
</style>
