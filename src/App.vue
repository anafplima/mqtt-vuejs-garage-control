<template>
  <div id="app">
    <div class="container">
      <h1>Garage</h1>
    </div>
    <div class="container">
      <h2>Control</h2>
    </div>
    <div class="container">
      <div class="row">
        <div class="col">
          <button type="button" v-on:click="openGarageDoor()" class="btn btn-primary">Open</button>
        </div>
        <div class="col">
          <button type="button" v-on:click="closeGarageDoor()" class="btn btn-secondary">Close</button>
        </div>
      </div>
    </div>
    <div class="container">
      <p>
        Garage:
        <span v-html="connected"></span>
      </p>
    </div>
    <div class="container">
      <p>
        Status:
        <span v-html="message"></span>
      </p>
    </div>
    <footer>
      <nav class="navbar navbar-dark bg-dark justify-content-center">
        <div class="row">
          <div class="col-12 text-center text-white">
            <p>&copy; Copyright- 2019 | Ana & Glenda</p>
          </div>
        </div>
      </nav>
    </footer>
  </div>
</template>

<script>
import "bootstrap/dist/css/bootstrap.css";

export default {
  name: "app",
  data() {
    return {
      garageState: "",
      connected: false,
      message: ""
    };
  },
  mounted() {
    this.$mqtt.subscribe("garage/connected");
    this.$mqtt.subscribe("garage/state");
    this.$mqtt.publish("garage/state/update");
  },
  mqtt: {
    "garage/connected": function(data) {
      //alert(topic + " : " + String.fromCharCode.apply(null, data));
      this.connected = data.toString() === "true";
      //alert(this.connected);
    },
    "garage/state"(data) {
      //alert(topic + " : " + String.fromCharCode.apply(null, data));
      this.garageState = data.toString();
      this.message = this.garageState;
    }
  },
  methods: {
    openGarageDoor() {
      if (!this.connected) {
        return alert("A garagem está desconectada.");
      }

      if (this.garageState == "open") {
        return alert("A garagem já está aberta.");
      }

      this.$mqtt.publish("garage/open", "true");
    },
    closeGarageDoor() {
      if (!this.connected) {
        return alert("A garagem está desconectada.");
      }

      if (this.garageState == "close") {
        return alert("A garagem já está fechada.");
      }

      this.$mqtt.publish("garage/close", "true");
    }
  }
};
</script>
<style scoped>
</style>