<template>
  <div id="app">
    <div class="container marginTop">
      <h4>
        Data COVID-19
        <small>by Diamsyah M Dida</small>
      </h4>
      <br />
      <div class="alert alert-success" role="alert">
        <h5 class="alert-heading">Update Terakhir</h5>
        <hr />
        <p class="mb-0">
          Data ini di update terakhir kali pada
          <strong>{{allCovid.lastUpdate}}</strong>
        </p>
      </div>
    </div>
    <br />
    <form class="container">
      <div class="form-group row">
        <div class="col-md-1"></div>
        <label for="selectCountry" class="col-3 col-md-1">
          <b>Country</b>
        </label>
        <select
          class="form-control col-6 col-md-8"
          v-model="country"
          @change="getCovidById"
          id="selectCountry"
        >
          <option
            v-for="data in countries"
            :key="data.id"
            v-bind:value="data.code"
          >{{data.country}} - {{data.code}}</option>
        </select>
        <div class="col-3 col-md-1">
          <button class="btn btn-secondary btn-block" @click="reset">
            <i class="fa fa-window-close"></i>
          </button>
        </div>
      </div>
    </form>
    <br />
    <button class="btn btn-primary" @click="showAll" v-if="!getAll">
      <i class="fa fa-eye"></i> Show All Data
    </button>
    <i v-if="getAll">Please wait...</i>
    <br />
    <br />
    <div class="container" v-if="selectCountry.confirmed == ''">
      <h4>Total Data</h4>
      <div class="row">
        <div class="col-12 col-md-4">
          <div class="card text-center">
            <div class="card-header text-light bg-info">
              Confirmed
              <i class="fa fa-check-square"></i>
            </div>
            <div class="card-body">
              <h3 class="card-title">{{allCovid.confirmed}}</h3>
            </div>
          </div>
        </div>
        <div class="col-12 col-md-4">
          <div class="card text-center">
            <div class="card-header text-light bg-success">
              Recovered
              <i class="fa fa-retweet"></i>
            </div>
            <div class="card-body">
              <h3 class="card-title">{{allCovid.recovered}}</h3>
            </div>
          </div>
        </div>
        <div class="col-12 col-md-4">
          <div class="card text-center">
            <div class="card-header text-light bg-danger">
              deaths
              <i class="fa fa-ambulance"></i>
            </div>
            <div class="card-body">
              <h3 class="card-title">{{allCovid.deaths}}</h3>
            </div>
          </div>
        </div>
      </div>
    </div>

    <div class="container" v-if="selectCountry.confirmed != ''">
      <div class="row">
        <div class="col-12 col-md-4">
          <div class="card text-center">
            <div class="card-header text-light bg-info">
              Confirmed
              <i class="fa fa-check-square"></i>
            </div>
            <div class="card-body">
              <h3 class="card-title">{{selectCountry.confirmed}}</h3>
            </div>
          </div>
        </div>
        <div class="col-12 col-md-4">
          <div class="card text-center">
            <div class="card-header text-light bg-success">
              Recovered
              <i class="fa fa-retweet"></i>
            </div>
            <div class="card-body">
              <h3 class="card-title">{{selectCountry.recovered}}</h3>
            </div>
          </div>
        </div>
        <div class="col-12 col-md-4">
          <div class="card text-center">
            <div class="card-header text-light bg-danger">
              Deaths
              <i class="fa fa-ambulance"></i>
            </div>
            <div class="card-body">
              <h3 class="card-title">{{selectCountry.deaths}}</h3>
            </div>
          </div>
        </div>
      </div>
    </div>
    <div class="container" v-if="allData.length != 0">
      <div class="table table-responsive">
        <table class="table table-bordered">
          <thead class="thead-light">
            <tr>
              <th>COUNTRY</th>
              <th>CONFIRMED</th>
              <th>RECOVERED</th>
              <th>DEATHS</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="all in allData" :key="all.attributes.OBJECTID">
              <td><b>{{all.attributes.Country_Region}}</b></td>
              <td>{{all.attributes.Confirmed}}</td>
              <td>{{all.attributes.Recovered}}</td>
              <td>{{all.attributes.Deaths}}</td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  </div>
</template>

<script>
import { BootstrapVue, BootstrapVueIcons } from "bootstrap-vue";
import axios from "axios";
import Vue from "vue";
import "bootstrap/dist/css/bootstrap.css";
import "bootstrap-vue/dist/bootstrap-vue.css";
// Install BootstrapVue
Vue.use(BootstrapVue);
// Optionally install the BootstrapVue icon components plugin
Vue.use(BootstrapVueIcons);
Vue.use(axios);
export default {
  name: "app",
  components: {},
  data() {
    return {
      // url api corona
      apiURL: "https://covid19.mathdro.id/api/",
      apiURL2: "https://api.kawalcorona.com/",

      // variable untuk get total data korban corona
      allCovid: {
        confirmed: "",
        recovered: "",
        deaths: "",
        lastUpdate: ""
      },

      // variable untuk get semua data negara
      index: null,
      countries: [],

      // variable untuk menyimpan hasil select negara
      country: "",

      // variable untuk get data korban corona by negara
      selectCountry: {
        confirmed: "",
        recovered: "",
        deaths: ""
      },

      // variable untuk get semua data corona dan negara
      allData: [],

      // variable loading
      getAll: false
    };
  },
  mounted() {
    this.getAllCovid();
    this.getCountries();
  },
  methods: {
    // get total data korban corona
    getAllCovid() {
      axios.get(this.apiURL).then(response => {
        this.allCovid.confirmed = response.data.confirmed.value;
        this.allCovid.recovered = response.data.recovered.value;
        this.allCovid.deaths = response.data.deaths.value;
        this.allCovid.lastUpdate = response.data.lastUpdate;
      });
    },

    // get semua data negara
    getCountries() {
      axios.get(this.apiURL + "countries").then(response => {
        let countries = response.data.countries;

        Object.entries(countries).map(([negara, kode]) => {
          let data = {
            id: this.index++,
            country: negara,
            code: kode
          };
          this.countries.push(data);
        });
      });
    },

    // get data korban corona by negara
    getCovidById() {
      axios.get(this.apiURL + "countries/" + this.country).then(
        response => {
          this.selectCountry.confirmed = response.data.confirmed.value;
          this.selectCountry.recovered = response.data.recovered.value;
          this.selectCountry.deaths = response.data.deaths.value;
        },
        error => {
          if (error.response) {
            alert("Data not found");
            location.reload();
          }
        }
      );
    },

    // reset data
    reset() {
      this.selectCountry.confirmed = "";
      this.getAllCovid();
    },

    // get semua data korban corona dan negara
    showAll() {
      this.getAll = true;
      axios.get(this.apiURL2).then(
        response => {
          this.getAll = false;
          this.allData = response.data;
        },
        error => {
          this.getAll = false;
          if (error.response) {
            alert("Data not found");
            location.reload();
          }
        }
      );
    }
  }
};
</script>

<style>
.marginTop {
  margin-top: 50px;
}

.col-md-4 {
  margin-bottom: 20px;
}

table {
  font-family: monospace;
}

#app {
  font-family: "Times New Roman", Times, serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
</style>
