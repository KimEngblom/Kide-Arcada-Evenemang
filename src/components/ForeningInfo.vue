<template>
<div class="container bg-light">
  <div class="row justify-content-center">
    <div id="main" v-bind:style="{ color: computedColor, background: computedBackground }">
      <!--<b-button class="backButton" variant="light" @click="goBack">Back</b-button>-->
      <button type="button" class="btn btn-warning btn-floating btn-lg" id="btn-back-to-top" @click="goBack">
        <!-- <i class="fas fa-arrow-left"></i>Font awesome arrow-->Back
      </button>
      <h1 class="text-center">{{this.forening}} Events</h1>
    </div>
  </div>
  
  <div v-if="amount !== 0">
    <div v-for="index in amount" v-bind:key="index" class="grid2 row justify-content-center"> <!-- index in amount för att max visa 3 evenemang (amount kan max vara 3) -->
      <div>
        <h1 class="underrubrik">{{this.apiData[index - 1].name}}</h1> <!-- Namn på evenemanget -->
      </div>
      <div class="row justify-content-center">
        
          <div class="col-lg-4 col-md-12"> <!-- div runt bilderna-->
            <img class="img-fluid" v-if="!this.apiData[index - 1].mediaFilename" @click="emitToParent(), setEvent($event)" :id="[apiData[index - 1].id]" :value="[index - 1]"
              :src="`https://portalvhdsp62n0yt356llm.blob.core.windows.net/bailataan-mediaitems/${this.apiData[index - 1].companyMediaFilename}`"/> <!-- Föreningens logo ifall banner är "null" (placeholder) -->
            <img class="img-fluid" v-else @click="emitToParent(), setEvent($event)" :id="[apiData[index - 1].id]" :value="[index - 1]"
              :src="`https://portalvhdsp62n0yt356llm.blob.core.windows.net/bailataan-mediaitems/${this.apiData[index - 1].mediaFilename}`"/> <!-- Banner -->
          </div>
          <div class="col-lg-4 col-md-8 my-auto"> <!--Korta info texten-->
            <p class="text-md-right">
              <ul class="list-inline mx-auto justify-content-center">
                <li><strong>Date: </strong>{{ this.apiData[index - 1].dateActualFrom }}</li> <!-- När evenemanget börjar -->
                <li><strong>Ticket drop: </strong>{{ this.apiData[index - 1].dateSalesFrom }}</li> <!-- När biljetterna släpps -->
                <li v-if="this.apiData[index - 1].salesEnded"><strong>Ticket sales ended: </strong>Yes</li> <!-- Om biljettförsäljningen slutat-->
                <li v-else><strong>Ticket sale ended: </strong>No</li> <!-- Om biljettförsäljningen INTE slutat-->
                <li v-if="this.apiData[index - 1].availability && !this.apiData[index - 1].salesEnded"><strong>Tickets available: </strong>Yes</li> <!-- Om det finns biljetter och salesEnded == false -->
                <li v-else-if="!this.apiData[index - 1].salesEnded"><strong>Tickets available: </strong>No</li> <!-- Om det INTE finns biljetter och salesEnded == false -->
              </ul>
            </p>
          </div>
          <div class="qr col-lg-4 col-md-4 my-auto"> <!--Qr koden-->
            <img :src="`https://api.qrserver.com/v1/create-qr-code/?data=https://kide.app/events/${this.apiData[index - 1].id}`" />
          </div>
      </div>
    </div>
  </div>

  <div v-else class="noEvents"> <!-- Om det inte finns någå evenemang -->
    <h1>No incoming events &#128557;</h1>
  </div>
</div>
</template>
  
<script>
export default {
  name: 'ForeningInfo',
  emits: ["update", "getEvent"],
  props: {
    currentTab: String,
    forening: String,
    apiData: Array,
    amount: Number,
    color: String,
    background: String,
    boolean: Boolean
  },
  computed: {
    computedColor: function () {
      return this.color;
    },
    computedBackground: function() {
      return this.background;
    }
  },
  methods: {
    emitToParent() {
      this.$emit("update", { tab: 'EventInfo', boolean: true})
    },
    setEvent(e) {
      this.$emit("getEvent", {eventId: e.target.id, value: e.target.getAttribute('value')})
    },
    goBack() { // Bakåtknappens funktion, ändrar synlig tab
      this.$emit("update", { tab: 'HelloWorld' })
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.grid2 {
  border-top: 0.5px solid #2c3e50;
  padding-bottom: 20px;
}

#btn-back-to-top {
  position: fixed;
  left: 20px;
  z-index: 1;
  width: 85px;
}

@media screen and (max-width: 1200px) {
  #btn-back-to-top {
    position: fixed;
    bottom: 10px;
    right: 10px;
    left: auto;
  }
}
.text-center {
  margin: 0;
}
.container {
  background: linear-gradient(to bottom, #e8f0fc, #c1d2ee, #a5c5e5, #9abfe2, #90bae0);  
  min-height: 100vh;
  min-width: 100%; 
}

.img-fluid {
  cursor: pointer;
}

#main {
  display: flex;
  justify-content: center;
  position: relative;
}

img {
  max-height: 180px;
  width: 320px;
  border-radius: 30px;
  margin-left: auto;
  border: 1px solid #555;
}

p {
  width: 325px;
  margin: auto;
}
.noEvents h1 {
  font-style: oblique;
  margin-left: 0;
  padding-top: 115px;
  padding-bottom: 115px;
  font-size: 50px;
}

ul {
  margin: auto;
  text-align: left;
  width: max-content;
}
.qr {
  height: 150px;
  margin-right: auto;
}
.qr img {
  border: none;
  width: 150px;
  height: 150px;
  border-radius: 0px;
}
.underrubrik {
  font-size: 30px;
  margin: auto;
  padding-top: 10px;
  padding-bottom: 10px;
  font-weight: bold;
}
.backButton{
  left: 20px;
  position: absolute;
}
</style>