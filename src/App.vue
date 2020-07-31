<template>
  <app-container top-nav-color="success" no-bars>
    <template #top-nav></template>
    <page>
      <div class="row">
        <div class="col-8">
          <full-calendar ref="fullcalendar" :options="this.calendarOptions" />
        </div>
        <div class="col-4">
          <vertical-dual-list-box :up-list="this.upList" :down-list="this.downList"></vertical-dual-list-box>
        </div>
      </div>
    </page>
  </app-container>
</template>

<script>
// main.js
import Vue from "vue";
//import vue-adminlte
import VueAdminLte from "@keenmate/vue-adminlte";
import "@keenmate/vue-adminlte/src/vue-adminlte-setup";
import JQuery from "jquery";
Vue.use(VueAdminLte);

//import fullcalendar and plugis
import FullCalendar from "@fullcalendar/vue";
import dayGridPlugin from "@fullcalendar/daygrid";
import interactionPlugin from "@fullcalendar/interaction";
import resourceTimelinePlugin from "@fullcalendar/resource-timeline";
import { formatDate } from "@fullcalendar/core";
//import { INITIAL_EVENTS, createEventId } from "./event-utils";
window.jQuery = JQuery;

//local components
import VerticalDualListBox from "./Components/VerticalDualListBox";

export default {
  name: "App",
  components: {
    FullCalendar, // make the <FullCalendar> tag available,
    VerticalDualListBox
  },
  data() {
    return {
      upList: ["Zuzana", "Marek", "Kuba"],
      downList: ["Petr", "Hanka", "Filip"],
      resourcesColors: []
    };
  },
  mounted() {
    this.resourcesColors = this.generateColor();
  },
  methods: {
    onSelect(arg) {
      var resource = arg.resource;
      var start = arg.start;
      var end = arg.end;
      var eventId = this.createEventId(resource, start, end);
      var event = {
        id: eventId,
        title: resource.title,
        start: start,
        end: end,
        resourceId: resource.id,
        color: this.getResourceColor(resource.id)
      };
      let calendarApi = arg.view.calendar;
      calendarApi.unselect();
      //console.log(arg);
      console.log(calendarApi);
      console.log(event);
      calendarApi.addEvent(event);
    },
    createEventId(resource, start, end) {
      var startstr = formatDate(start, {
        month: "2-digit",
        day: "numeric",
        hour: "2-digit",
        minute: "2-digit",
        locale: "en"
      });
      var endtstr = formatDate(end, {
        month: "2-digit",
        day: "numeric",
        hour: "2-digit",
        minute: "2-digit",
        locale: "en"
      });
      return "r:" + resource.id + "||s:" + startstr + "||e:" + endtstr;
    },
    getResourceColor(resource) {
      console.log(resource);
      var color;
      this.resourcesColors.find(c => {
        if (c.name === resource) {
          color = c.color;
          return true;
        }
      });
      return color;
    },
    generateColor() {
      var v = [];
      var joinedList = this.upList.concat(this.downList);
      joinedList.forEach(e => {
        var randomColor =
          "#" + Math.floor(Math.random() * 16777215).toString(16);
        v.push({ name: e, color: randomColor });
      });

      return v;
    },
    eventDropHandler(arg) {
      console.log("drop reverted");
      if (arg.newResource != arg.oldResource) arg.revert();
    }
  },
  computed: {
    calendarOptions() {
      console.log("calendar options updated");
      return {
        schedulerLicenseKey: "CC-Attribution-NonCommercial-NoDerivatives",
        plugins: [dayGridPlugin, interactionPlugin, resourceTimelinePlugin],
        initialView: "resourceTimeline",
        headerToolbar: {
          left: "prev,next today",
          center: "title",
          right: "dayGridMonth,timeGridWeek,timeGridDay"
        },
        selectable: true,
        select: this.onSelect,
        //dateClick: this.handleDateClick,
        resources: this.resourcesComputed,
        editable: true,
        eventDrop: this.eventDropHandler
      };
    },
    resourcesComputed() {
      console.log("resources updated");
      return this.upList.map(x => ({
        id: x,
        title: x
      }));
    }

    // ,
    // calendarEvents() {
    //   console.log("EventsList in calendar oprions updated");
    //   console.log(this.EventsList);
    //   return this.EventsList;
    // }
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
