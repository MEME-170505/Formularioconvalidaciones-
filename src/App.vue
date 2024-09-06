<template>
  <div id="app">
    <h1>Formulario de Registro</h1>

    <!-- Aquí va el formulario para agregar una persona -->
    <!-- @submit.prevent evita que la página se recargue cuando envíes el formulario -->
    <form @submit.prevent="addPerson">
      <div>
        <label for="name">Nombre:</label>
        <!-- v-model une el campo de texto con 'person.name' -->
        <input v-model="person.name" type="text" id="name" required />
        <!-- Muestra el error si hay uno -->
        <span v-if="errors.name" class="error">{{ errors.name }}</span>
      </div>

      <div>
        <label for="age">Edad:</label>
        <!-- v-model une este campo de número con 'person.age' -->
        <input v-model="person.age" type="number" id="age" required min="1" />
        <span v-if="errors.age" class="error">{{ errors.age }}</span>
      </div>

      <div>
        <label>Género:</label>
        <!-- Radios para seleccionar el género, todos se vinculan a 'person.gender' -->
        <input type="radio" id="niño" value="Niño" v-model="person.gender" required />
        <label for="niño">Niño</label>
        <input type="radio" id="niña" value="Niña" v-model="person.gender" required />
        <label for="niña">Niña</label>
        <input type="radio" id="niñe" value="Niñe" v-model="person.gender" required />
        <label for="niñe">Niñe</label>
        <span v-if="errors.gender" class="error">{{ errors.gender }}</span>
      </div>

      <div>
        <label for="matricula">Matrícula:</label>
        <!-- Campo de texto para la matrícula -->
        <input v-model="person.matricula" type="text" id="matricula" required />
        <span v-if="errors.matricula" class="error">{{ errors.matricula }}</span>
      </div>

      <div>
        <label for="email">Correo:</label>
        <!-- Campo para el correo, usando v-model -->
        <input v-model="person.email" type="email" id="email" required />
        <span v-if="errors.email" class="error">{{ errors.email }}</span>
      </div>

      <div>
        <label for="phone">Número de teléfono:</label>
        <!-- Campo para el teléfono con validación de 10 dígitos -->
        <input v-model="person.phone" type="tel" id="phone" required pattern="[0-9]{10}" />
        <span v-if="errors.phone" class="error">{{ errors.phone }}</span>
      </div>

      <div>
        <label for="career">Carrera:</label>
        <!-- Campo para la carrera -->
        <input v-model="person.career" type="text" id="career" required />
        <span v-if="errors.career" class="error">{{ errors.career }}</span>
      </div>

      <!-- Botón para enviar el formulario -->
      <button type="submit">Agregar</button>
    </form>

    <!-- Botón para borrar todos los datos, solo aparece si hay datos -->
    <button @click="clearData" v-if="people.length" class="clear-button">Borrar todos los datos</button>

    <!-- Tabla para mostrar los datos si hay personas registradas -->
    <table v-if="people.length">
      <thead>
        <tr>
          <th>Nombre</th>
          <th>Edad</th>
          <th>Género</th>
          <th>Matrícula</th>
          <th>Correo</th>
          <th>Teléfono</th>
          <th>Carrera</th>
        </tr>
      </thead>
      <tbody>
        <!-- Recorremos la lista de personas y mostramos sus datos -->
        <tr v-for="(person, index) in people" :key="index">
          <td>{{ person.name }}</td>
          <td>{{ person.age }}</td>
          <td>{{ person.gender }}</td>
          <td>{{ person.matricula }}</td>
          <td>{{ person.email }}</td>
          <td>{{ person.phone }}</td>
          <td>{{ person.career }}</td>
        </tr>
      </tbody>
    </table>

    <!-- Mensaje si no hay datos registrados -->
    <p v-else>No hay datos registrados.</p>
  </div>
</template>

<script>
export default {
  data() {
    return {
      // Aquí van los datos del formulario
      person: {
        name: '',
        age: '',
        gender: '',
        matricula: '',
        email: '',
        phone: '',
        career: ''
      },
      // Lista para guardar a las personas que registramos
      people: [],
      // Aquí van los mensajes de error para cada campo
      errors: {
        name: '',
        age: '',
        gender: '',
        matricula: '',
        email: '',
        phone: '',
        career: ''
      }
    }
  },
  mounted() {
    // Cuando la página se carga, intentamos cargar los datos guardados
    const savedPeople = localStorage.getItem('people');
    if (savedPeople) {
      this.people = JSON.parse(savedPeople); // Convertimos el string guardado a objeto
    }
  },
  methods: {
    // Función para validar el formulario antes de agregar una persona
    validateForm() {
      // Reseteamos los errores antes de validar
      this.errors = {
        name: '',
        age: '',
        gender: '',
        matricula: '',
        email: '',
        phone: '',
        career: ''
      };

      let isValid = true; // Suponemos que todo está bien al principio

      // Validamos cada campo del formulario
      if (!this.person.name) {
        this.errors.name = 'El nombre es obligatorio';
        isValid = false;
      }

      if (!this.person.age || this.person.age <= 0) {
        this.errors.age = 'La edad debe ser mayor que 0';
        isValid = false;
      }

      if (!this.person.gender) {
        this.errors.gender = 'Debes seleccionar un género';
        isValid = false;
      }

      if (!this.person.matricula) {
        this.errors.matricula = 'La matrícula es obligatoria';
        isValid = false;
      }

      if (!this.person.email || !this.validateEmail(this.person.email)) {
        this.errors.email = 'Debes ingresar un correo válido';
        isValid = false;
      }

      if (!this.person.phone || !this.person.phone.match(/^[0-9]{10}$/)) {
        this.errors.phone = 'Debes ingresar un número de teléfono de 10 dígitos';
        isValid = false;
      }

      if (!this.person.career) {
        this.errors.career = 'La carrera es obligatoria';
        isValid = false;
      }

      return isValid; // Devolvemos si todo es válido o no
    },
    // Función para validar el formato del correo electrónico
    validateEmail(email) {
      const re = /\S+@\S+\.\S+/;
      return re.test(email); // Verifica si el email tiene el formato correcto
    },
    // Función para agregar una persona a la lista
    addPerson() {
      if (this.validateForm()) {
        this.people.push({ ...this.person }); // Añadimos una copia de los datos
        localStorage.setItem('people', JSON.stringify(this.people)); // Guardamos en localStorage

        // Limpiamos el formulario después de agregar los datos
        this.person = {
          name: '',
          age: '',
          gender: '',
          matricula: '',
          email: '',
          phone: '',
          career: ''
        };
      }
    },
    // Función para borrar todos los datos guardados
    clearData() {
      this.people = []; // Vaciamos la lista
      localStorage.removeItem('people'); // Borramos los datos de localStorage
    }
  }
}
</script>

<style>
/* Estilos básicos para que la página se vea bien */
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  text-align: center;
  margin-top: 40px;
}

form {
  margin-bottom: 20px;
}

form div {
  margin-bottom: 10px;
}

button {
  background-color: #42b983;
  color: white;
  border: none;
  padding: 10px 20px;
  font-size: 16px;
  cursor: pointer;
  border-radius: 5px;
}

button:hover {
  background-color: #38a169;
}

.clear-button {
  margin-top: 20px;
  background-color: #e74c3c;
}

table {
  margin: 20px auto;
  border-collapse: collapse;
  width: 80%;
}

table, th, td {
  border: 1px solid black;
}

th, td {
  padding: 10px;
  text-align: center;
}

thead {
  background-color: #f2f2f2;
}

.error {
  color: red;
  font-size: 12px;
  margin-left: 10px;
}
</style>
