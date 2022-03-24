<template>
  <v-container>
    <div class="form text-center">
      <v-form>
        <v-text-field label="Ime" v-model="ime"></v-text-field>
        <v-btn @click="dohvat()">Potvrdi</v-btn></v-form
      >
    </div>
    <v-data-table
      dense
      :headers="headers"
      :items="podaci"
      item-key="name"
      class="elevation-1"
    ></v-data-table
  ></v-container>
</template>

<script>
// @ is an alias to /src
import axios from "axios";
export default {
  name: "Home",
  data() {
    return {
      headers: [
        {
          text: "Ime",
          align: "start",
          sortable: false,
          value: "ime",
        },
        { text: "Države i vjerojatnosti", value: "nacionalnost" },
        { text: "Godine", value: "godina" },
        { text: "Spol i vjerojatnosti", value: "spol" },
      ],
      podaci: [],
      ime: "",
      okej1: "",
      okej2: "",
      okej3: "",
    };
  },
  mounted() {},
  methods: {
    async dohvat() {
      const varijabla1 = await axios.get(
        "https://api.agify.io?name=" + this.ime
      );
      this.okej1 = varijabla1.data;

      const varijabla2 = await axios.get(
        "https://api.genderize.io/?name=" + this.ime
      );
      this.okej2 = varijabla2.data;

      const varijabla3 = await axios.get(
        "https://api.nationalize.io/?name=" + this.ime
      );
      this.okej3 = varijabla3.data;

      let svaki = "";
      this.okej3.country.forEach((drzava) => {
        svaki =
          svaki +
          " " +
          drzava.country_id +
          " " +
          (drzava.probability * 100).toFixed(2) +
          "%";
      });

      this.podaci.push({
        ime: this.ime,
        godina: this.okej1.age,
        spol: this.okej2.gender + " " + this.okej2.probability * 100 + "%",
        nacionalnost: svaki,
      });
      console.log(this.okej3);
      console.log(svaki);
    },
  },
};
</script>

<style>
.form {
  display: flex;
  margin: 0, auto;
  justify-content: center;
}
</style>
