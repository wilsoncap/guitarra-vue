<script setup>
import {ref, reactive, onMounted, watch} from 'vue'
import {db} from './data/gutarras'
import Guitarra from './components/Guitarra.vue'
import Header from './components/Header.vue'
import Footer from './components/Footer.vue'

// cuando es un arreglo o datos agrupados es mejor usar recative



// string boleano u arreglo
const guitarras = ref(db);
const carrito = ref([]);
const guitarra = ref({});

watch(carrito, () =>{
    guardarLocalStorage();
},{
    deep:true
})

onMounted(() =>{
    // console.log('Componete Listo');
    guitarras.value = db
    guitarra.value = db[3]

    const carritoStorage = localStorage.getItem('carrito')
    if (carritoStorage) {
        carrito.value = JSON.parse(carritoStorage)
    }
    
})

const guardarLocalStorage = () => {
    localStorage.setItem('carrito', JSON.stringify(carrito.value));
}

const agregarCarrito = (guitarra)=>{
        const existeCarrito = carrito.value.findIndex(producto => producto.id === guitarra.id)
        console.log(existeCarrito);

        if (existeCarrito >= 0) {
            carrito.value[existeCarrito].cantidad++
            
        }else{
            guitarra.cantidad = 1
            carrito.value.push(guitarra)   
        }

        
    }

const decrementarCantidad = (id) =>{
    const index = carrito.value.findIndex(producto => producto.id === id);
    if (carrito.value[index].cantidad <=1) return;
    carrito.value[index].cantidad--;

}

const incrementarCantidad = (id) =>{
    const index = carrito.value.findIndex(producto => producto.id === id);
    if (carrito.value[index].cantidad >=5) return;
    carrito.value[index].cantidad++;

}

const eliminarProducto = (id) =>{ 
    carrito.value = carrito.value.filter(producto => producto.id !== id)

}

const vaciarCarrito = () => {
    carrito.value = [];

}

</script>

<template>
  <Header 
    :carrito = "carrito"
    :guitarra ="guitarra"
    @incrementar-cantidad = "incrementarCantidad"
    @decrementar-cantidad = "decrementarCantidad"
    @agregar-carrito = "agregarCarrito"
    @eliminar-producto = "eliminarProducto"
    @vaciar-carrito = "vaciarCarrito"

  />

    <main class="container-xl mt-5">
        <h2 class="text-center">Nuestra Colección</h2>

        <div class="row mt-5">
           

            <guitarra
                 v-for="guitarra in guitarras"
                 v-bind:key="guitarra.id"
                v-bind:guitarra="guitarra"
                @agregar-carrito="agregarCarrito"
            />

        </div>
    </main>

<Footer />
    
</template>

