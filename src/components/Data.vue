<template>
  <h2>Dernière mesure:</h2>
  <div>
    <div v-for="mesure in currentMesure" :key="mesure._id">
      <h4>Humidité dans le sol : {{mesure.humiditesol}}</h4>
      <h4>Humidité dans l'air : {{mesure.humiditeaire}}</h4>
      <h4>Température de l'air : {{mesure.temperaturaire}}</h4>
      <h4>Luminosité : {{mesure.luminosite}}</h4>
    </div>
    <br />
    <div>
      <div v-if="role === 'admin'">
        <button @click="confirmSendDownlink">Send test message</button>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';
import { fetchLastMesure } from '../api';
import {userRole, SendDownlink} from "/src/api.js";
export default {
  data() {
    return {
      token: localStorage.getItem('token'),
      mesures: [],
      role: '',
    };
  },
  computed: {
    currentMesure() {
      return this.mesures;
    },
  },
  async mounted() {
    await this.fetchLastMesure();
    this.role = await userRole();
  },
  methods: {
    async fetchLastMesure() {
      const { data } = await axios.get('https://test-pi2.onrender.com/mesures/last', {
        headers: {
          'Authorization': `Bearer ${this.token}`,
        }});
      this.mesures = data;
    },
    async confirmSendDownlink() {
      if(confirm("Are you sure you want to send 'test' to the MKRWAN ?")) {
        await this.SendDownlink();
      }
    },
    async SendDownlink() {
      let response = await SendDownlink("test");
      if(response.success) {
        alert('Message envoyé !');
      } else {
        console.log(response.success);
        alert('An error occurred, please try again later');
      }
    },
  },
};
</script>