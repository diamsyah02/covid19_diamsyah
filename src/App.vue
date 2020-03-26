<template>
  <div id="app">
    <div class="container marginTop">
      <h4>Data COVID-19</h4>
      <pre>by</pre>
      <h5><pre>Diamsyah M Dida</pre></h5>
      <br />
      <div class="alert alert-primary" role="alert">
        <h5 class="alert-heading">Pembaharuan!</h5>
        <hr />
        <p class="mb-0">
          Data ini di perbaharui pada:
          <strong>{{allCovid.lastUpdate}}</strong>
        </p>
      </div>
    </div>
    <br />
    <form class="container">
      <div class="form-group row">
        <div class="col-md-1"></div>
        <label for="selectCountry" class="col-3 col-md-1">
          <b>Negara</b>
        </label>
        <select
          class="form-control col-6 col-md-8"
          v-model="country"
          @change="getCovidById"
          id="selectCountry"
        >
          <option value>-- Pilih negara --</option>
          <option
            v-for="data in countries"
            :key="data.iso2"
            v-bind:value="data.iso2"
          >{{data.name}} - {{data.iso2}}</option>
        </select>
        <div class="col-3 col-md-1">
          <span class="badge badge-secondary" @click="reset">
            <!-- <i class="fa fa-window-close"></i> -->
            Reset
          </span>
        </div>
      </div>
    </form>
    <div class="container" v-if="selectCountry.confirmed == ''">
      <h4>Total Data</h4>
      <div class="row">
        <div class="col-12 col-md-4">
          <div class="card text-center">
            <div class="card-header text-light bg-info">
              Terkonfirmasi
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
              Sembuh
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
              Meninggal
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
              Terkonfirmasi
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
              Sembuh
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
              Meninggal
              <i class="fa fa-ambulance"></i>
            </div>
            <div class="card-body">
              <h3 class="card-title">{{selectCountry.deaths}}</h3>
            </div>
          </div>
        </div>
      </div>
    </div>
    <div class="container">
      <h4>Tampilkan Data</h4>
      <div class="row">
        <div class="col-6 col-md-6">
          <button class="btn btn-primary" @click="showAll" v-if="!getAll">Se-dunia</button>
          <i v-if="getAll">Tunggu bentar...</i>
        </div>
        <div class="col-6 col-md-6">
          <button
            class="btn btn-warning text-light"
            @click="showByProvinsiIndo"
            v-if="!getByProv"
          >Indonesia Saja</button>
          <i v-if="getByProv">Tunggu bentar...</i>
        </div>
      </div>
    </div>
    <div class="container" v-if="allData.length != 0">
      <div class="table table-responsive">
        <table class="table table-bordered">
          <thead class="thead-light">
            <tr>
              <th>NEGARA</th>
              <th>TERKONFIRMASI</th>
              <th>SEMBUH</th>
              <th>MENINGGAL</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="all in allData" :key="all.attributes.OBJECTID">
              <td>
                <b>{{all.attributes.Country_Region}}</b>
              </td>
              <td>{{all.attributes.Confirmed}}</td>
              <td>{{all.attributes.Recovered}}</td>
              <td>{{all.attributes.Deaths}}</td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
    <div class="container" v-if="byProv.length != 0">
      <div class="table table-responsive">
        <table class="table table-bordered">
          <thead class="thead-light">
            <tr>
              <th>PROVINSI</th>
              <th>TERKONFIRMASI</th>
              <th>SEMBUH</th>
              <th>MENINGGAL</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="prov in byProv" :key="prov.attributes.FID">
              <td>
                <b>{{prov.attributes.Provinsi}}</b>
              </td>
              <td>{{prov.attributes.Kasus_Posi}}</td>
              <td>{{prov.attributes.Kasus_Semb}}</td>
              <td>{{prov.attributes.Kasus_Meni}}</td>
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
import VueAxios from "vue-axios";
import "bootstrap/dist/css/bootstrap.css";
import "bootstrap-vue/dist/bootstrap-vue.css";
// Install BootstrapVue
Vue.use(BootstrapVue);
// Optionally install the BootstrapVue icon components plugin
Vue.use(BootstrapVueIcons);
Vue.use(VueAxios, axios);
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

      // variable untuk get data corona by provinsi di indo
      byProv: [],

      // variable loading
      getAll: false,
      getByProv: false
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
        this.countries = response.data.countries;
      });
    },

    // get data korban corona by negara
    getCovidById() {
      if (this.country == "") {
        this.reset();
      } else {
        axios.get(this.apiURL + "countries/" + this.country).then(
          response => {
            this.selectCountry.confirmed = response.data.confirmed.value;
            this.selectCountry.recovered = response.data.recovered.value;
            this.selectCountry.deaths = response.data.deaths.value;
          },
          error => {
            if (error.response) {
              alert("Data not found");
              this.reset();
            }
          }
        );
      }
    },

    // reset data
    reset() {
      this.selectCountry.confirmed = "";
      this.country = '';
      this.getAllCovid();
    },

    // get semua data korban corona dan negara
    showAll() {
      this.getAll = true;
      this.byProv = [];
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
    },

    // get data korban corona by provinsi di indonesia
    showByProvinsiIndo() {
      this.getByProv = true;
      this.allData = [];
      axios.get("https://api.kawalcorona.com/indonesia/provinsi/").then(
        response => {
          this.getByProv = false;
          this.byProv = response.data;
        },
        error => {
          this.getByProv = false;
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
body {
  background-color: #fff;
}
.marginTop {
  margin-top: 50px;
}

.col-md-4,
.col-6 {
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

.badge{
  padding: 8px 10px 8px 10px;
  margin-top: 4px;
  cursor: pointer;
}
</style>
