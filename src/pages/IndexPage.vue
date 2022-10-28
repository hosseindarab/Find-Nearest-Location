<template>
  <q-page class="flex flex-center">
    <q-form @submit="onSubmit" class="q-gutter-md">
      <q-avatar
        size="170px"
        font-size="92px"
        color="teal"
        text-color="white"
        icon="local_pharmacy"
      />
      <q-input
        name="address"
        v-model="address"
        color="primary"
        label="Location"
        filled
        clearable
        bottom-slots
      >
        <template v-slot:prepend>
          <q-icon name="place" />
        </template>
        <template v-slot:hint>
          Enter the coordinator of the location for example: [45.459839, 9.147159]
        </template>
      </q-input>
      <div class="q-gutter-sm">
        <q-btn @click="getData" label="Find" type="submit" color="primary" />
        <q-btn class="btn btn-sm btn-warning ml-2" @click="clearGetOutput">Clear</q-btn>
      </div>
    </q-form>
    <q-card v-if="submitResult.length > 0" flat bordered class="q-mt-md bg-grey-2">
      <q-card-section>The nearest pharmacy is in: </q-card-section>
      <q-separator />
      <q-card-section class="row q-gutter-sm items-center">
        <div
          class="q-px-sm q-py-xs bg-grey-8 text-white rounded-borders text-center text-no-wrap"
        >
          <div v-if="json" class="alert alert-secondary mt-2" role="alert">
            <pre>{{ pharmacyName }}</pre>
          </div>
        </div>
      </q-card-section>
    </q-card>
  </q-page>
</template>

<script>
import { ref, defineComponent } from "vue";
import { api } from "boot/axios";
import { useQuasar } from "quasar";

export default defineComponent({
  name: "IndexPage",
  data() {
    return {
      json: null,
      loading: true,
      pharmacyName: "",
    };
  },
  methods: {
    getData() {
      const $q = useQuasar();
      api
        .get(
          "https://dati.comune.milano.it/dataset/7e18f0d3-b7f1-49b7-969d-da2c04131dd6/resource/1d71ca1e-a6b2-4984-8e35-a0bd9163bbca/download/ds501_elenco_farmacie_milano_final.json"
        )
        .then((response) => {
          this.json = response.data;
          this.$q.notify({
            type: "positive",
            message: "Success!",
          });
        })
        .catch((e) => {
          this.$q.notify({
            color: "negative",
            postition: "top",
            message: e.toString(),
            icon: "report_problem",
          });
        })
        .finally(() => {
          this.loading = false;
        });
    },
    calculateDistance() {
      function distance(lat1, lon1, lat2, lon2, unit) {
        var radlat1 = (Math.PI * lat1) / 180;
        var radlat2 = (Math.PI * lat2) / 180;
        var theta = lon1 - lon2;
        var radtheta = (Math.PI * theta) / 180;
        var dist =
          Math.sin(radlat1) * Math.sin(radlat2) +
          Math.cos(radlat1) * Math.cos(radlat2) * Math.cos(radtheta);
        if (dist > 1) {
          dist = 1;
        }
        dist = Math.acos(dist);
        dist = (dist * 180) / Math.PI;
        dist = dist * 60 * 1.1515;
        if (unit == "K") {
          dist = dist * 1.609344;
        }
        if (unit == "N") {
          dist = dist * 0.8684;
        }
        return dist;
      }

      var poslat = 1.28210155945393;
      var poslng = 103.81722480263163;

      for (var i = 0; i < this.json.length; i++) {
        // if this location is within 0.1KM of the user, add it to the list
        if (distance(poslat, poslng, this.json[i].LAT, this.json[i].LNG, "K") <= 0.1) {
          this.pharmacyName = this.json[i].DENOM_FARMACIA;
          console.log(this.json[i].DENOM_FARMACIA)
        }
      }
    },
    clearGetOutput() {
      this.json = null;
    },
  },
  setup() {
    const $q = useQuasar();
    const submitResult = ref([]);
    return {
      address: ref(),
      submitResult,

      onSubmit(evt) {
        const formData = new FormData(evt.target);
        const data = [];

        for (const [address, value] of formData.entries()) {
          data.push({
            address,
            value,
          });
        }

        submitResult.value = data;
      },
    };
  },
});
</script>
