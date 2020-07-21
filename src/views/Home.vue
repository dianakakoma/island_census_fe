<template>
  <div class="home">
    <h1>{{ message }}</h1>
    <h2>All the Island's Beings</h2>
    <!--Index Action  -->
    <div v-for="being in beings">
      <h3>{{ being.name }} the {{ being.being_type }}</h3>
      <p>Id: {{ being.id }}</p>

      <!--Show Action -->
      <button v-on:click="showBeing(being)">Show More...</button>
      <div v-if="currentBeing === being">
        <p>Habitat: {{ being.habitat }}</p>
        <p>Weight: {{ being.weight }} lbs.</p>
        <p>Known Parents: {{ being.known_parents }}</p>
        <p>First Impression: {{ being.first_impression }}</p>
        <!--update/patch action -->

        <h4>Update {{ being.name }}'s information</h4>
        <div>
          Weight (lbs)
          <input type="integer" v-model="being.weight" />
          <br />
          Parentage
          <input type="string" v-model="being.known_parents" />
          <br />
          <button v-on:click="updateBeing(being)" id="update-btn">Update being</button>
        </div>
        <!-- Destroy Action -->
        <button id="destroy-btn" v-on:click="destroyBeing(being)">Destroy this being</button>
      </div>
    </div>
    <!--create action -->
    <div>
      <h3>Add a New Being</h3>
      Being Type
      <input type="text" v-model="newBeingType" />
      <br />
      Name:
      <input type="text" v-mode="newName" />
      <br />
      Habitat:
      <select v-model="newHabitat">
        <option disabled value="">Please select One (and only one)</option>
        <option>air</option>
        <option>land</option>
        <option>sea</option>
      </select>
      <br />
      Known Parents?
      <input type="checkbox" id="checkbox" v-model="newKnownParents" />
      <label for="checkbox">Yes</label>
      <br />
      Weight:
      <input type="integer" v-model="newWeight" />
      <br />
      First Impression of this being:
      <input type="text" v-model="newFirstImpression" />
      <button v-on:click="createBeing()">Create New Being</button>
    </div>
  </div>
</template>

<style></style>

<script>
import axios from "axios";
export default {
  data: function() {
    return {
      message: "Welcome to the Island Census",
      beings: [],
      currentBeing: {},
      newName: "",
      newBeingType: "",
      newHabitat: "",
      newKnownParents: "",
      newFirstImpression: "",
      newWeight: ""
    };
  },
  created: function() {
    axios.get("/api/beings").then((response) => {
      this.beings = response.data;
    });
  },
  methods: {
    showBeing: function(being) {
      if (this.currentBeing === being) {
        this.currentBeing = {};
      } else {
        this.currentBeing = being;
      }
    },
    updateBeing: function(being) {
      var params = {
        weight: being.weight,
        known_parents: being.known_parents
      };
      axios.patch("/api/beings/" + being.id, params).then((response) => {
        this.currentBeing = {};
      });
    },
    createBeing: function() {
      var params = {
        being_type: this.newBeingType,
        name: this.newName,
        habitat: this.newHabitat,
        weight: this.newWeight,
        known_parents: this.newKnownParents,
        first_impression: this.newFirstImpression
      };
      axios.post("api/beings", params).then((response) => {
        this.beings.push(response.data);
        this.newBeingType = "";
        this.newName = "";
        this.newHabitat = "";
        this.newWeight = "";
        this.newKnownParents = "";
        this.newFirstImpression = "";
      });
    },
    destroyBeing: function(being) {
      axios.delete("api/beings/" + being.id).then((response) => {
        var index = this.beings.indexOf(being);
        this.beings.splice(index, 1);
      });
    }
  }
};
</script>
