<script setup>
import { ref, computed } from "vue";

const nombre = ref("");
const peso = ref("");
const estatura = ref(""); // en cm
const registros = ref([]);

const imc = computed(() => {
  if (
    peso.value &&
    estatura.value &&
    parseFloat(peso.value) > 0 &&
    parseFloat(estatura.value) > 0
  ) {
    const h = parseFloat(estatura.value) / 100; // convertir a metros
    return (parseFloat(peso.value) / (h * h)).toFixed(2);
  }
  return null;
});

const categoriaIMC = computed(() => {
  if (!imc.value) return null;
  const b = parseFloat(imc.value);
  if (b < 18.5) return "bajo";
  if (b < 25) return "normal";
  if (b < 30) return "sobrepeso";
  return "obesidad";
});

const colorIMC = computed(() => {
  switch (categoriaIMC.value) {
    case "bajo":
      return "yellow";
    case "normal":
      return "green";
    case "sobrepeso":
      return "orange";
    case "obesidad":
      return "red";
    default:
      return "black";
  }
});

const imagenCategoria = computed(() => {
  switch (categoriaIMC.value) {
    case "bajo":
      return "https://via.placeholder.com/150x150?text=Bajo+Peso";
    case "normal":
      return "https://via.placeholder.com/150x150?text=Peso+Normal";
    case "sobrepeso":
      return "https://via.placeholder.com/150x150?text=Sobrepeso";
    case "obesidad":
      return "https://via.placeholder.com/150x150?text=Obesidad";
    default:
      return "";
  }
});

const guardarRegistro = () => {
  if (!nombre.value.trim()) {
    alert("El nombre no puede estar vacío.");
    return;
  }
  if (!peso.value || parseFloat(peso.value) <= 0) {
    alert("El peso debe ser mayor a 0.");
    return;
  }
  if (!estatura.value || parseFloat(estatura.value) <= 0) {
    alert("La estatura debe ser mayor a 0.");
    return;
  }
  registros.value.push({
    numero: registros.value.length + 1,
    nombre: nombre.value,
    peso: parseFloat(peso.value),
    estatura: parseFloat(estatura.value),
    imc: imc.value,
  });
  // Limpiar formulario
  nombre.value = "";
  peso.value = "";
  estatura.value = "";
};

const eliminarRegistro = (index) => {
  if (confirm("¿Estás seguro de que quieres eliminar este registro?")) {
    registros.value.splice(index, 1);
    // Reasignar números de registro
    registros.value.forEach((reg, i) => {
      reg.numero = i + 1;
    });
  }
};
</script>

<template>
  <div class="app">
    <header class="header">
      <h1>Calculadora de IMC</h1>
      <p>
        Calcula tu Índice de Masa Corporal de forma sencilla y guarda tus
        registros.
      </p>
    </header>

    <div class="container">
      <div class="form-section">
        <h2>Datos Personales</h2>
        <form @submit.prevent="guardarRegistro" class="form">
          <div class="form-group">
            <label for="nombre">Nombre:</label>
            <input
              id="nombre"
              v-model="nombre"
              type="text"
              placeholder="Ingresa tu nombre"
              required
            />
          </div>
          <div class="form-group">
            <label for="peso">Peso (kg):</label>
            <input
              id="peso"
              v-model="peso"
              type="number"
              step="0.1"
              min="0.1"
              placeholder="Ej: 70.5"
              required
            />
          </div>
          <div class="form-group">
            <label for="estatura">Estatura (cm):</label>
            <input
              id="estatura"
              v-model="estatura"
              type="number"
              step="0.1"
              min="0.1"
              placeholder="Ej: 170.0"
              required
            />
          </div>
          <button type="submit" class="btn-calculate">
            Calcular y Guardar
          </button>
        </form>
      </div>

      <div v-if="imc" class="result-section">
        <h2>Resultado del IMC</h2>
        <div class="result-card">
          <div class="imc-display">
            <span class="imc-value" :style="{ color: colorIMC }">{{
              imc
            }}</span>
            <span class="imc-category" :style="{ color: colorIMC }">{{
              categoriaIMC
            }}</span>
          </div>
          <img
            v-if="imagenCategoria"
            :src="imagenCategoria"
            alt="Categoría IMC"
            class="category-image"
          />
        </div>
      </div>

      <div class="records-section">
        <h2>Historial de Registros</h2>
        <div class="table-container">
          <table class="records-table">
            <thead>
              <tr>
                <th># Registro</th>
                <th>Nombre</th>
                <th>Peso (kg)</th>
                <th>Estatura (cm)</th>
                <th>IMC</th>
                <th>Acciones</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="(registro, index) in registros" :key="registro.numero">
                <td>{{ registro.numero }}</td>
                <td>{{ registro.nombre }}</td>
                <td>{{ registro.peso }}</td>
                <td>{{ registro.estatura }}</td>
                <td>{{ registro.imc }}</td>
                <td>
                  <button @click="eliminarRegistro(index)" class="btn-delete">
                    Eliminar
                  </button>
                </td>
              </tr>
              <tr v-if="registros.length === 0">
                <td colspan="6" class="no-records">
                  No hay registros aún. Calcula tu IMC para comenzar.
                </td>
              </tr>
            </tbody>
          </table>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.app {
  min-height: 100vh;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  font-family: "Segoe UI", Tahoma, Geneva, Verdana, sans-serif;
  color: #333;
}

.header {
  text-align: center;
  padding: 40px 20px;
  color: white;
}

.header h1 {
  margin: 0;
  font-size: 2.5rem;
  font-weight: 300;
}

.header p {
  margin: 10px 0 0 0;
  font-size: 1.1rem;
  opacity: 0.9;
}

.container {
  max-width: 1200px;
  margin: 0 auto;
  padding: 0 20px 40px 20px;
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 30px;
}

.form-section,
.result-section,
.records-section {
  background: white;
  border-radius: 15px;
  padding: 30px;
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.1);
}

.form-section {
  grid-column: 1 / -1;
}

.form-section h2,
.result-section h2,
.records-section h2 {
  margin-top: 0;
  color: #4a5568;
  font-weight: 600;
  margin-bottom: 25px;
}

.form {
  display: grid;
  grid-template-columns: 1fr 1fr 1fr auto;
  gap: 20px;
  align-items: end;
}

.form-group {
  display: flex;
  flex-direction: column;
}

label {
  margin-bottom: 8px;
  font-weight: 500;
  color: #4a5568;
}

input {
  padding: 12px 16px;
  border: 2px solid #e2e8f0;
  border-radius: 8px;
  font-size: 16px;
  transition: border-color 0.3s ease;
}

input:focus {
  outline: none;
  border-color: #667eea;
  box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

input::placeholder {
  color: #a0aec0;
}

.btn-calculate {
  padding: 12px 24px;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: white;
  border: none;
  border-radius: 8px;
  font-size: 16px;
  font-weight: 600;
  cursor: pointer;
  transition: transform 0.2s ease, box-shadow 0.2s ease;
}

.btn-calculate:hover {
  transform: translateY(-2px);
  box-shadow: 0 5px 15px rgba(102, 126, 234, 0.4);
}

.btn-delete {
  padding: 6px 12px;
  background: #e53e3e;
  color: white;
  border: none;
  border-radius: 6px;
  font-size: 14px;
  font-weight: 500;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

.btn-delete:hover {
  background: #c53030;
}

.result-card {
  text-align: center;
  padding: 20px;
  background: #f7fafc;
  border-radius: 12px;
  border: 2px solid #e2e8f0;
}

.imc-display {
  margin-bottom: 20px;
}

.imc-value {
  font-size: 3rem;
  font-weight: bold;
  display: block;
  margin-bottom: 5px;
}

.imc-category {
  font-size: 1.2rem;
  text-transform: uppercase;
  letter-spacing: 1px;
}

.category-image {
  max-width: 150px;
  border-radius: 10px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
}

.table-container {
  overflow-x: auto;
}

.records-table {
  width: 100%;
  border-collapse: collapse;
  background: white;
  border-radius: 8px;
  overflow: hidden;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
}

.records-table th {
  background: #4a5568;
  color: white;
  padding: 15px;
  text-align: left;
  font-weight: 600;
  text-transform: uppercase;
  font-size: 0.9rem;
  letter-spacing: 0.5px;
}

.records-table td {
  padding: 15px;
  border-bottom: 1px solid #e2e8f0;
}

.records-table tbody tr:hover {
  background: #f7fafc;
}

.no-records {
  text-align: center;
  color: #a0aec0;
  font-style: italic;
  padding: 40px;
}

@media (max-width: 768px) {
  .container {
    grid-template-columns: 1fr;
    gap: 20px;
  }

  .form {
    grid-template-columns: 1fr;
  }

  .header h1 {
    font-size: 2rem;
  }

  .imc-value {
    font-size: 2.5rem;
  }
}
</style>
