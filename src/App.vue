<template>
  <div id="app">
    <FullCalendar :options="calendarOptions" />
  </div>
</template>

<script>
// main.js
import Vue from "vue";
import VueAdminLte from "@keenmate/vue-adminlte";
// import code of original (mostly) adminlte project
import "@keenmate/vue-adminlte/src/vue-adminlte-setup";
import JQuery from "jquery";
Vue.use(VueAdminLte);
import FullCalendar from "@fullcalendar/vue";
import dayGridPlugin from "@fullcalendar/daygrid";
import interactionPlugin from "@fullcalendar/interaction";
window.jQuery = JQuery;
export default {
  name: "App",
  components: {
    FullCalendar // make the <FullCalendar> tag available
  },
  data() {
    return {
      calendarOptions: {
        plugins: [dayGridPlugin, interactionPlugin],
        initialView: "dayGridMonth"
      }
    };
  }
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
