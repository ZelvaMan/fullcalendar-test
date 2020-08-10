<template>
  <app-container top-nav-color="success" no-bars>
    <template #top-nav></template>
    <page>
      <div class="row">
        <div class="col-8">
          <full-calendar ref="fullcalendar" :options="this.calendarOptions" />
        </div>
        <div class="col-4">
          <card title="vertical dual list box" is-info>
            <vertical-dual-list-box :up-list="this.upList" :down-list="this.downList"></vertical-dual-list-box>
          </card>
          <card title="slot min and max time">
            <div class="rm-2 lm-2">
              <div class="form-group">
                <label class="lp-2">day start</label>
                <input type="text" class="form-control" v-model.lazy="slotMinTime" />
              </div>
              <div class="form-group">
                <label class="lp-2">day end</label>
                <input type="text" class="form-control" v-model.lazy="slotMaxTime" />
              </div>
            </div>
          </card>
          <card title="position selector">
            <multiselect
              :multiple="false"
              :options="upList"
              :close-on-select="true"
              v-model="selectedResource"
            ></multiselect>
            <multiselect
              :multiple="false"
              :options="positions"
              :close-on-select="true"
              v-model="selectedPosition"
            ></multiselect>
            <button
              class="btn btn-primary align-right"
              type="button"
              v-on:click="changePossition"
            >change</button>
          </card>
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
import timeGridPlugin from "@fullcalendar/timegrid";
import csLocale from "@fullcalendar/core/locales/cs";
//import { INITIAL_EVENTS, createEventId } from "./event-utils";

//others
window.jQuery = JQuery;
import moment from "moment";
Vue.prototype.moment = moment;
import Multiselect from "vue-multiselect";

//local components
import VerticalDualListBox from "./Components/VerticalDualListBox";

export default {
  name: "App",
  components: {
    FullCalendar, // make the <FullCalendar> tag available,
    VerticalDualListBox,
    Multiselect
  },
  data() {
    return {
      upList: ["Zuzana", "Marek", "Kuba"],
      downList: ["Petr", "Hanka", "Filip"],
      resources: [{}],
      resourcesColors: [],
      slotMinTime: "04:00",
      slotMaxTime: "22:00",
      selectedResource: "",
      positions: ["chef", "pizza", "kp", "bar", "servis", "kitchen"],
      selectedPosition: ""
    };
  },
  mounted() {
    this.resourcesColors = this.generateColor();
    this.resources = this.generateResources();
  },
  methods: {
    isAfter(date1, date2) {
      return moment(date1).isAfter(date2);
    },
    isBefore(date1, date2) {
      return !this.isAfter(date1, date2);
    },
    hourOccupied(resourceEvents, start, end) {
      var r = false;

      resourceEvents.forEach(rEvent => {
        if (
          this.isAfter(start, rEvent.start) &&
          this.isBefore(start, rEvent.end)
        ) {
          r = true;
        }
        if (this.isAfter(end, rEvent.start) && this.isBefore(end, rEvent.end)) {
          r = true;
        }
        if (this.isBefore(start, rEvent.start) && this.isAfter(end, rEvent.end))
          r = true;
      });
      return r;
    },
    onSelect(arg) {
      let calendarApi = arg.view.calendar;
      var resource = arg.resource;
      var start = arg.start;
      var end = arg.end;
      var resorceEvents = calendarApi.getResourceById(resource.id).getEvents();
      calendarApi.unselect();
      console.log(resource);
      if (!this.hourOccupied(resorceEvents, start, end)) {
        var eventId = this.createEventId(resource, start, end);
        var event = {
          id: eventId,
          title: resource.id,
          start: start,
          end: end,
          resourceId: resource.id,
          color: this.getResourceColor(resource.id)
        };
        calendarApi.addEvent(event);
      }
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
      var color;
      this.resourcesColors.find(c => {
        if (c.name === resource) {
          color = c.color;
          return true;
        }
      });
      return color;
    },
    generateResources() {
      var r = [];
      this.upList.forEach(x => {
        r.push({ id: x, name: x, position: "kitchen" });
      });
      this.downList.forEach(x => {
        r.push({ id: x, name: x, position: "pizza" });
      });
      return r;
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
    },
    changePossition() {
      this.resources.forEach(r => {
        if (r.id == this.selectedResource) {
          r.position = this.selectedPosition;
          return;
        }
      });
    }
  },
  computed: {
    calendarOptions() {
      console.log("calendar options updated");
      return {
        schedulerLicenseKey: "CC-Attribution-NonCommercial-NoDerivatives",
        slotDuration: "00:15",
        plugins: [
          dayGridPlugin,
          interactionPlugin,
          resourceTimelinePlugin,
          timeGridPlugin
        ],
        initialView: "resourceTimeline",
        headerToolbar: {
          left: "prev,next today",
          center: "title",
          right:
            "resourceTimelineDay,resourceTimelineWeek,resourceTimelineMonth"
        },
        businessHours: {
          // days of week. an array of zero-based day of week integers (0=Sunday)
          daysOfWeek: [1, 2, 3, 4], // Monday - Thursday

          startTime: "10:00", // a start time (10am in this example)
          endTime: "18:00" // an end time (6pm in this example)
        },
        slotMinWidth: 15,
        resourceAreaColumns: [
          {
            group: true,
            field: "position",
            headerContent: "position"
          },
          {
            field: "name",
            headerContent: "employe"
          }
        ],
        titleFormat: { year: "numeric", month: "2-digit", day: "2-digit" },
        selectable: true,
        select: this.onSelect,
        //dateClick: this.handleDateClick,
        resources: this.resourcesComputed,
        editable: true,
        eventDrop: this.eventDropHandler,
        slotMinTime: this.slotMinTime,
        slotMaxTime: this.slotMaxTime,
        locale: csLocale
      };
    },
    resourcesComputed() {
      console.log("resources updated");
      var resources = [];
      this.resources.forEach(r => {
        if (
          this.upList.find(res => {
            if (res == r.id) return true;
          })
        ) {
          resources.push(r);
        }
      });
      return resources;
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

<style src="vue-multiselect/dist/vue-multiselect.min.css"></style>
