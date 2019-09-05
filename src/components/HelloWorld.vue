<template>
  <div>
    <div class="notification-header">
      <div class="message connecting" v-if="isConnecting">Conectando...</div>

      <div class="message connected" v-if="connected">Conectado com sucesso</div>

      <div class="message connection-error" v-if="connectionError">Falha ao conectar</div>
    </div>

    <div class="form-container">
      <form @submit.prevent="sendFood">
        <div>
          <label for="food">Comida: </label>
          <input id="food" type="text" v-model="food" class="form-control">
        </div>
        <button type="submit">Send</button>
      </form>
    </div>

    <div>
      <ul>
        <li v-for="(food, index) in foods" :key="index">{{ food.name }}</li>
      </ul>
    </div>
  </div>
</template>

<script>
import io from 'socket.io-client';

export default {
  name: 'HelloWorld',
  props: {
    msg: String
  },
  data() {
    return {
      socket: io('localhost:3000'),
      food: '',
      foods: [],
      isConnecting: true,
      connected: false,
      disconnected: false,
      connectionError: false
    }
  },
  methods: {
    sendFood() {
      this.socket.emit('foods', { name: this.food });

      this.food = '';
    }
  },
  mounted() {
    this.socket.on('connect', () => {
      this.isConnecting = false;
      this.connected = true;
      this.connectionError = false;

      this.socket.on('disconnect', () => {
        this.connected = false;
        this.disconnected = true;
      });
    });

    this.socket.on('connect_error', () => {
      this.isConnecting = false;
      this.connected = false;
      this.connectionError = true;
    });

    this.socket.on('foods', data => this.foods = [data, ...this.foods]);
  }
}
</script>

<style scoped>
.message {
  padding: 15px 0;
}

.connected {
  color: white;
  background: #3CB371	;
}

.connecting {
  background: #D3D3D3;
}

.connection-error {
  color: white;
  background: #FF4500;
}

.form-container {
  margin: 70px 0;
}
</style>
