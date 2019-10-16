<template>
  <div class="container">
    <h1>New Actor</h1>
    <div>
      First Name:
      <input type="text" v-model="newActorFirstName" />
      Last Name:
      <input type="text" v-model="newActorLastName" />
      Age:
      <input type="text" v-model="newActorAge" />
      known For:
      <input type="text" v-model="newActorKnownFor" />

      <button v-on:click="createActor()">Create Actor</button>
    </div>
    <h1>All Actors</h1>
    <div v-for="actor in actors">
      <h2>{{ actor.first_name }}</h2>
      <h2>{{ actor.last_name }}</h2>
      <button v-on:click="showActor(actor)">Show more</button>
      <div v-if="currentActor === actor">
        <p>Known For: {{ actor.known_for }}</p>
        <div>
          First Name:
          <input type="text" v-model="actor.first_name" />
          Last Name:
          <input type="text" v-model="actor.last_name" />
          Age:
          <input type="text" v-model="actor.age" />
          <button v-on:click="updateActor(actor)">Update Actor</button>
          <button v-on:click="destroyActor(actor)">Delete Actor</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  data: function() {
    return {
      actors: [],
      currentActor: {},
      newActorFirstName: "",
      newActorLastName: "",
      newActorAge: ""
    };
  },
  created: function() {
    axios.get("/api/actors").then(response => {
      this.actors = response.data;
    });
  },
  methods: {
    createActor: function() {
      var params = {
        first_name: this.newActorFirstName,
        last_name: this.newActorLastName,
        age: this.newActorAge
      };
      axios.post("/api/actors", params).then(response => {
        this.actors.push(response.data);
        this.newActorFirstName = "";
        this.newActorLastName = "";
        this.newActorAge = "";
      });
    },
    showActor: function(actor) {
      if (this.currentActor === actor) {
        this.currentActor = {};
      } else {
        this.currentActor = actor;
      }
    },
    updateActor: function(actor) {
      var params = {
        first_name: actor.first_name,
        last_name: actor.last_name,
        age: actor.age
      };
      axios.patch("/api/actors/" + actor.id, params).then(response => {
        this.currentActor = {};
      });
    },
    destroyActor: function(actor) {
      axios.delete("/api/actors/" + actor.id).then(response => {
        var index = this.actors.indexOf(actor);
        this.actors.splice(index, 1);
      });
    }
  }
};
</script>
