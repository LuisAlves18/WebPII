<template>
  <div id="offers">
    <div
      class="bg-image headerContent justify-content-center"
      style="
        height: 60vh;
        width: 100vw;
        background-image: url('https://res.cloudinary.com/dsfbhbeyt/image/upload/v1619953742/carousel1_jktwrb.webp');
        background-attachment: fixed;
        background-position: center;
        background-repeat: no-repeat;
        background-size: cover;
      "
    >
      <!--     @submit.prevent="filterOffers()"
 -->
      <b-form @submit.prevent="filterOffers()">
        <p class="offersTitle">Encontre o seu trabalho aqui</p>

        <div class="headerDropdowns row justify-content-lg-center">
          <div class="typeDrop col-lg-4">
            <label for="#dropdownType"> Tipo de Oferta</label>
            <div class="dropdown" id="dropdownType">
              <b-select
                id="dropdownMenuButton"
                class="btn dropdown-toggle buttonHeader offerType"
                v-model="send.typeOfferId"
              >
                <option
                  v-for="type in this.$store.state.offers_type"
                  :key="type.id"
                  :value="type.id"
                >
                  {{ type.description }}
                </option>
              </b-select>
            </div>
          </div>
          <div class="cursoDrop col-lg-4">
            <label for="#dropdownCurso">Curso Frequentado</label>
            <b-form-select
              id="dropdownMenuButton"
              class="btn dropdown-toggle buttonHeader offerCourse"
              v-model="send.areaId"
            >
              <option
                v-for="area in this.$store.state.areas"
                :key="area.id"
                :value="area.id"
              >
                {{ area.description }}
              </option>
            </b-form-select>
          </div>
          <div class="companyFilter col-lg-4">
            <label for="searchCompany">Empresa</label>
            <input
              v-model="send.name"
              type="text"
              id="searchCompany"
              class="form-control filterCompanyInput"
              placeholder="Empresa"
              aria-label="Empresa"
            />
          </div>
          <b-button type="submit" class="filterButton mt-5">Filtrar</b-button>
        </div>
      </b-form>
    </div>

    <!-- <router-link to="/offers/jobs">Ofertas Profissionais</router-link> |
    <router-link to="/offers/internships">Estágios</router-link> |
    <router-link to="/offers/freelance">Freelance</router-link> |
    <router-link to="/offers/events">Eventos</router-link> |
    <router-link to="/offers/workshops">Workshops</router-link> -->
    <b-container class="containerOffers mt-5">
      <tr
        v-for="offer in filteredOffers"
        :key="offer.id"
        class="d-flex justify-content-center"
      >
        <b-card
          class="cardOffer mb-4 border-0"
          :img-src="offer.company.logo"
          img-left
        >
          <b-card-body>
            <div>
              <b-card-title class="d-flex justify-content-left">{{
                offer.company.name
              }}</b-card-title>
              <b-card-sub-title class="d-flex justify-content-left mb-1">{{
                getAreaById(offer.areaId)
              }}</b-card-sub-title>
              <b-card-text class="d-flex justify-content-left mt-3">
                <ul>
                  <p class="text-left">
                    <i class="fas fa-list iconOffer"></i>
                    {{ getTypeById(offer.typeOfferId) }}
                  </p>

                  <p class="text-left">
                    <i class="fas fa-map-marker-alt iconOffer"></i>
                    {{ offer.company.address }}
                  </p>
                </ul>
              </b-card-text>
            </div>

            <div class="text-left">
              <router-link
                id="seeMoreOffer"
                :to="{
                  name: 'OffersView',
                  params: { OfferId: offer.id },
                }"
                tag="b-button"
              >
                Ver Mais
              </router-link>
            </div>
          </b-card-body>
        </b-card>
      </tr>
    </b-container>
  </div>
</template>
<script>
/* eslint-disable */
export default {
  name: "Offers",
  components: {},
  data() {
    return {
      send: {
        typeOfferId: "",
        areaId: "",
        name: "",
      },
      checkedType: [],
      checkedArea: [],
    };
  },
  mounted() {
    this.storeOffers();

    /* this.getAllCompanies() */
    this.getAllOffersType();
    this.getAllAreas();
  },
  created() {},
  methods: {
    async storeOffers() {
      await this.$store.dispatch("fetchAllOffers");
      console.log(this.$store.state.offers);
    },
    /* async getAllCompanies(){
      await this.$store.dispatch("fetchAllCompanies");
    }, */
    async getAllAreas() {
      await this.$store.dispatch("fetchAllAreas");
      console.log("areas", this.$store.state.areas);
    },
    async getAllOffersType() {
      await this.$store.dispatch("fetchAllOffersType");
    },
    async filterOffers() {
      /* console.log("send",this.send)
     this.req=""
      if(this.send.typeOfferId.length>0){
        this.req+=`type=${this.send.typeOfferId}&`
      }
      if(this.send.areaId.length>0){
        this.req+=`area=${this.send.areaId}&`
      }
      if(this.send.name.length>0){
        this.req+=`name=${this.send.name}`
      }
      await this.$store.dispatch("fetchFilteredOffers",this.req) */
      return this.$store.state.offers.filter((offer) => {
        let filterOffersType = true;
        let filterOffersArea = true;
        let checkedTypeLth = this.send.typeOfferId.length;
        let checkedAreaLth = this.send.areaId.length;
        if (checkedTypeLth != 0) {
          for (let i = 0; i < checkedTypeLth; i++) {
            if (offer.typeOfferId == this.send.typeOfferId) {
              filterOffersType = true;
            } else {
              filterOffersType = false;
            }
          }
        } else {
          filterOffersType = true;
        }
        if (checkedAreaLth != 0) {
          for (let i = 0; i < checkedAreaLth; i++) {
            if (offer.areaId == this.send.areaId) {
              filterOffersArea = true;
            } else {
              filterOffersArea = false;
            }
          }
        } else {
          filterOffersArea = true;
        }
        return filterOffersType && filterOffersArea;
      });
    },
    getLogobyId(id) {
      return this.$store.state.companies.find((company) => company.id === id)
        .logo;
    },
    getCompanyById(id) {
      return this.$store.state.companies.find((company) => company.id === id)
        .name;
    },
    getAreaById(id) {
      return this.$store.state.areas.find((area) => area.id === id).description;
    },
    getTypeById(id) {
      return this.$store.state.offers_type.find((type) => type.id === id)
        .description;
    },
    getLocationById(id) {
      return this.$store.state.companies.find((company) => company.id === id)
        .address;
    },
  },

  computed: {
    filteredOffers() {
      return this.$store.state.offers.filter((offer) => {
        let filterOffersType = true;
        let filterOffersArea = true;
        let checkedTypeLth = this.send.typeOfferId.length;
        let checkedAreaLth = this.send.areaId.length;
        if (checkedTypeLth != 0) {
          for (let i = 0; i < checkedTypeLth; i++) {
            if (offer.typeOfferId == this.send.typeOfferId) {
              filterOffersType = true;
            } else {
              filterOffersType = false;
            }
          }
        } else {
          filterOffersType = true;
        }
        if (checkedAreaLth != 0) {
          for (let i = 0; i < checkedAreaLth; i++) {
            if (offer.areaId == this.send.areaId) {
              filterOffersArea = true;
            } else {
              filterOffersArea = false;
            }
          }
        } else {
          filterOffersArea = true;
        }
        return filterOffersType && filterOffersArea;
      });
    },
  },
};
</script>

<style>
template,
html {
  overflow-x: hidden;
}
.buttonHeader {
  border-radius: 5px;
  background-color: rgb(225, 93, 68) !important;
  color: white !important;
  border: none;
}

.buttonHeader:hover {
  color: white;
}

.offerType {
  width: 200px !important;
}

.offerCourse {
  width: 200px !important;
}

.filterButton{
  background-color: #e2583e;
  color: white;
  border: none;

}

.filterButton:hover{
  background-color: #e2583e;
}

.buttonHeader:focus {
  outline: none;
  box-shadow: none;
}

.dropdown-item {
  cursor: pointer;
}

.offersTitle {
  font-size: 3.5vmax;
  font-weight: bold;
  color: white;
  padding-top: 35px;
}

.typeDrop label {
  color: white;
  font-size: 3vmin;
}

.cursoDrop label {
  color: white;
  font-size: 3vmin;
}

.companyFilter label {
  color: white;
  font-size: 3vmin;
}

.filterCompanyInput {
  width: 200px !important;
  margin-left: auto;
  margin-right: auto;
}

.filterCard {
  height: 25%;
}

ul {
  padding: 0;
}

.offersFilter {
  justify-content: center;
}
.radioLabel {
  padding-left: 5px;
}
.cardOffer {
  box-shadow: rgba(15, 76, 129, 0.4) 0px 10px 20px,
    rgba(200, 200, 200, 0.23) 0px 6px 6px;
  height: 17rem;
}
.iconOffer {
  color: #e2583e !important;
}

#seeMoreOffer {
  background-color: #0f4c81;
}
</style>
