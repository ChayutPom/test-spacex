<template>
  <div class="container">
    <ul class="nav nav-pills nav-fill">
      <li class="nav-item" v-for="(tab, index) in tabs" :key="index">
        <a
          class="nav-link"
          :class="{ active: activeTab === tab }"
          @click="activeTab = tab"
          aria-current="page"
          href="#"
          >{{ tab }}</a
        >
      </li>
    </ul>

    <div>
      <div
        class="card pt-3"
        v-b-modal.modal-1
        v-for="(laun, index) in launches"
        :key="index"
        @click="showDetail(laun)"
      >
        <div class="card-body">
          <div class="row">
            <div class="col" style="text-align: left">
              <img :src="laun.links.patch.large" alt="" style="width: 5%" />
              <span>{{ laun.name }}</span>
            </div>
            <div class="col" style="text-align: right">
              <span
                class="badge rounded-pill bg-primary"
                v-if="laun.crew.length > 0"
                >{{ laun.crew.length }} crew</span
              >
              <span>{{ laun.date_local }}</span>
              <span v-if="laun.upcoming === true">Upcomming</span>
              <span v-if="laun.upcoming === false">Launched</span>
            </div>
          </div>
        </div>
      </div>

      <b-modal id="modal-1" :hide-header="true" :hide-footer="true" style="text-align: center;">
        <div v-if="crewdata.length > 0" >
          <span>Crew-{{ crewdata.length }}</span> <br />
          <span>{{ detail.date_local }}</span>
          <hr />

          <span class="badge rounded-pill bg-primary">Crew</span>

          <div class="row">
            <div class="col" v-for="(crew, index) in crewdata" :key="index">
              <img
                :src="crew.image"
                alt=""
                style="
                  vertical-align: middle;
                  width: 50px;
                  height: 50px;
                  border-radius: 50%;
                "
              /><br />
              <span>{{ crew.name }}</span>
            </div>
          </div>
        </div>

        <span class="badge rounded-pill bg-primary">Rocket</span>
        <span>{{rocket.name}}</span><br>
        <img style="width: 60%;" v-for="(rocketimg, index) in rocket.flickr_images" :key="index" :src="rocketimg" alt="">
      </b-modal>
    </div>
  </div>
</template>

<script>
import axios from "axios";
export default {
  name: "spacex",
  props: {},

  data: function () {
    return {
      tabs: ["All", "Launched", "Upcomming"],
      activeTab: "All",
      launches: [],
      launchpad: [],
      rocket: [],
      crewdata: [],
      detail: [],
    };
  },

  mounted() {
    axios
      .get("https://api.spacexdata.com/v4/launches")
      .then((response) => (this.launches = response.data));
  },

  watch: {
    activeTab: function () {
      console.log(this.activeTab);

      if (this.activeTab === "All") {
        axios
          .get("https://api.spacexdata.com/v4/launches")
          .then((response) => (this.launches = response.data));
      } else if (this.activeTab === "Launched") {
        axios
          .get("https://api.spacexdata.com/v4/launches/past")
          .then((response) => (this.launches = response.data));
      }
      if (this.activeTab === "Upcomming") {
        axios
          .get("https://api.spacexdata.com/v4/launches/upcoming")
          .then((response) => (this.launches = response.data));
      }
    },
  },
  methods: {
    async showDetail(laun) {
      console.log(`https://api.spacexdata.com/v4/launchpads/${laun.launchpad}`);
      console.log(laun.launchpad);
      this.detail = laun;

      await axios
        .get(`https://api.spacexdata.com/v4/launchpads/${laun.launchpad}`)
        .then((response) => (this.launchpad = response.data));

      await axios
        .get(`https://api.spacexdata.com/v4/rockets/${laun.rocket}`)
        .then((response) => (this.rocket = response.data));

      if (laun.crew.length > 0) {
        for (let i = 0; i < laun.crew.length; i++) {
          console.log();
          await axios
            .get(` https://api.spacexdata.com/v4/crew/${laun.crew[i]}`)
            .then((response) => this.crewdata.push(response.data));
        }
      }
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
</style>
