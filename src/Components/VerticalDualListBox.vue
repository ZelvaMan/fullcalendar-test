<template>
  <div class="container">
    <div class="form-group w-100">
      <label>{{title}}</label>
      <div class="form-group buttons">
        <select
          multiple="multiple"
          id="upSelect"
          name="_helper1"
          style="height: 102px;"
          class="form-control"
          @change="oneTodowns"
          ref="upSelect"
        >
          <option v-for="UpItem in UpList" :key="UpItem">{{ UpItem }}</option>
        </select>
      </div>
      <div class="w-100"></div>
      <div class="form-group row">
        <div class="col-6">
          <button
            type="button"
            class="btn btn-block text-center btn-outline-secondary d-flex"
            title="Move all"
            v-on:click="allToDown"
          >
            <b class="text-center">\/</b>
          </button>
        </div>
        <div class="col-6">
          <button
            type="button"
            class="btn btn-block text-center btn-outline-secondary d-flex"
            title="Remove all"
            v-on:click="allToUp"
          >
            <b class="text-center">/\</b>
          </button>
        </div>
      </div>
      <div class="w-100"></div>
      <div class="form-group buttons">
        <select
          multiple="multiple"
          id="downsSelect"
          @change="oneToUp"
          name="_helper2"
          style="height: 102px;"
          class="form-control"
          ref="downSelect"
        >
          <option v-for="downsItem in DownList" :key="downsItem">{{ downsItem }}</option>
        </select>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "dualListBox",
  props: {
    /**
     * @type {string}
     * @description ID used for input and label
     */
    id: String,

    /**
     * @type {array},
     * @description content of Up List
     */
    UpList: {
      type: Array,
      default: () => {
        return ["Set upwnlist prop"];
      }
    },
    /**
     * @type {array},
     * @description content of downs List
     */
    DownList: {
      type: Array,
      default: () => {
        return ["Set Downlist prop"];
      }
    },
    /**
     * @type {string}
     * @description Title of DualListBox
     */
    title: String
  },
  methods: {
    oneTodowns() {
      var select = this.$refs.upSelect.value;
      if (select != "" || select != null) {
        this.DownList.push(select);
        var del;
        this.UpList.forEach((el, index) => {
          if (el === select) del = index;
        });
        this.UpList.splice(del, 1);
      }
      this.$refs.upSelect.value = "";
      this.emitChange();
    },
    oneToUp() {
      var select = this.$refs.downSelect.value;

      if (select != "" || select != null) {
        this.UpList.push(select);
        var del;
        this.DownList.forEach((el, index) => {
          if (el === select) del = index;
        });
        this.DownList.splice(del, 1);
      }
      this.$refs.downSelect.value = "";
      this.emitChange();
    },
    allToDown() {
      this.UpList.forEach(el => {
        this.DownList.push(el);
      });
      this.UpList.splice(0, this.UpList.length);
      this.emitChange();
    },
    allToUp() {
      this.DownList.forEach(el => {
        this.UpList.push(el);
      });
      this.DownList.splice(0, this.DownList.length);
      this.emitChange();
    },
    sortArrays() {
      this.UpList.sort(this.compare);
      this.DownList.sort(this.compare);
    },
    compare(a, b) {
      if (a.name < b.name) return -1;
      if (a.name > b.name) return 1;
      return 0;
    },
    emitChange() {
      this.$emit("input", { UpList: this.UpList, DownList: this.DownList });
      console.log("VerticalDualListBox input");
    }
  },
  data() {
    return {
      downSelectValue: "",
      UpSelectValue: ""
    };
  }
};
</script>