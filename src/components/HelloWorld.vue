<template>
  <div class="container bg-light">
    <div class="row justify-content-center">
      <div id="main bg-warning">
        <h1>Arcada Events</h1> <!-- Header -->
      </div>
    </div>

    <div class="content">
      <div class="row">
        <div class="ask col-lg-4 col-md-6">
          <img @click="emitToParent(), setForening($event)" class="org img-fluid" name="ask" src="../assets/asklogo2.png">
          <p v-if="this.apiDataAsk.length != 1">{{ this.apiDataAsk.length }} events</p>
          <p v-else>1 event</p>
        </div>
        <div class="tlk col-lg-4 col-md-6">
          <img @click="emitToParent(), setForening($event)" class="org img-fluid" name="tlk" src="../assets/tlklogo.png">
          <p v-if="this.apiDataTlk.length != 1">{{ this.apiDataTlk.length }} events</p>
          <p v-else>1 event</p>
        </div>
        <div class="hosk col-lg-4 col-md-6">
          <img @click="emitToParent(), setForening($event)" class="org img-fluid" name="hosk"
            src="../assets/hosklogo.png">
          <p v-if="this.apiDataHosk.length != 1">{{ this.apiDataHosk.length }} events</p>
          <p v-else>1 event</p>
        </div>

        <div class="comm col-lg-4 col-md-6">
          <img @click="emitToParent(), setForening($event)" class="org" name="comm" src="../assets/commlogo.png">
          <p v-if="this.apiDataComm.length != 1">{{ this.apiDataComm.length }} events</p>
          <p v-else>1 event</p>
        </div>
        <div class="hanse col-lg-4 col-md-6">
          <img @click="emitToParent(), setForening($event)" class="org" name="hanse" src="../assets/hanselogo.png">
          <p v-if="this.apiDataHanse.length != 1">{{ this.apiDataHanse.length }} events</p>
          <p v-else>1 event</p>
        </div>
        <div class="kult col-lg-4 col-md-6">
          <img @click="emitToParent(), setForening($event)" class="org" name="kult" src="../assets/kultlogo.png">
          <p v-if="this.apiDataKult.length != 1">{{ this.apiDataKult.length }} events</p>
          <p v-else>1 event</p>
        </div>
      </div>
      <hr class="line">

    </div>
    <div id="textDiv">
      <div class="incoming">
        <h5><strong>Incoming events</strong></h5> <!-- De nästa 3 kommande evenemangen -->
        <ul>
          <li v-for="index in list" v-bind:key="index" class="inc"> <!-- Nästa kommande evenemang (max 3)-->
            <p class="events" @click="emitToParentEvent(), setEvent($event)" :value="[index - 1]">{{ this.apiInfo[index -
              1].name }} - {{ this.apiInfo[index - 1].dateActualUntil }}</p>
            <!-- dateActualUntil så att vi inte ändrar på samma objekt som används i ForeningInfo, temporary fix?-->
          </li>
          <!-- Om det inte finns något kommande evenemang -->
          <li v-if="list == 0" class="noEvents">No incoming events &#128557;</li>
        </ul>
        <hr class="line">
      </div>
    </div>

    <div id="footer"> <!-- Copyright footer, syns endast på HelloWorld sidan -->
      <a href="https://forms.gle/TZdp32B2L8w3n2DF6" target="_blank">Feedback form</a>
      <p>&#169; Ivars Engblom Gröning Häggblom 2023</p>
    </div>
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  emits: ["update", "forening", "getEvent"],
  props: {
    msg: String,
    currentTab: String,
    isHidden: Boolean,
    apiInfo: Array,
    list: Number,
    apiDataTlk: Array,
    apiDataAsk: Array,
    apiDataComm: Array,
    apiDataHanse: Array,
    apiDataHosk: Array,
    apiDataKult: Array
  },
  methods: {
    emitToParent() {
      this.$emit("update", { tab: 'ForeningInfo' })
    },
    setForening(e) { // Visar endast de events som hör till föreningen
      let orgName = e.target.getAttribute('name')
      if (orgName === "ask") {
        this.$emit("forening", orgName)
      } else if (orgName === "tlk") {
        this.$emit("forening", orgName)
      } else if (orgName === "hosk") {
        this.$emit("forening", orgName)
      } else if (orgName === "comm") {
        this.$emit("forening", orgName)
      } else if (orgName === "hanse") {
        this.$emit("forening", orgName)
      } else if (orgName === "kult") {
        this.$emit("forening", orgName)
      }
    },
    setEvent(e) {
      let orgName = this.apiInfo[e.target.getAttribute('value')].companyName

      if (orgName.toUpperCase() === "ARCADA STUDERANDEKÅR - ASK") {
        orgName = "ask"
        this.$emit("getEvent", { eventId: this.apiInfo[e.target.getAttribute('value')].id, value: e.target.getAttribute('index') })
        this.$emit("forening", orgName)
      } else if (orgName.toUpperCase() === "TEKNISKA LÄROVERKETS KAMRATFÖRBUND R.F.") {
        orgName = "tlk"
        this.$emit("getEvent", { eventId: this.apiInfo[e.target.getAttribute('value')].id, value: e.target.getAttribute('index') })
        this.$emit("forening", orgName)
      } else if (orgName.toUpperCase() === "HOSK RF") {
        orgName = "hosk"
        this.$emit("getEvent", { eventId: this.apiInfo[e.target.getAttribute('value')].id, value: e.target.getAttribute('index') })
        this.$emit("forening", orgName)
      } else if (orgName.toUpperCase() === "COMMEDIA RF") {
        orgName = "comm"
        this.$emit("getEvent", { eventId: this.apiInfo[e.target.getAttribute('value')].id, value: e.target.getAttribute('index') })
        this.$emit("forening", orgName)
      } else if (orgName.toUpperCase() === "HANSE SF") {
        orgName = "hanse"
        this.$emit("getEvent", { eventId: this.apiInfo[e.target.getAttribute('value')].id, value: e.target.getAttribute('index') })
        this.$emit("forening", orgName)
      } else if (orgName.toUpperCase() === "KULT RF") {
        orgName = "kult"
        this.$emit("getEvent", { eventId: this.apiInfo[e.target.getAttribute('value')].id, value: e.target.getAttribute('index') })
        this.$emit("forening", orgName)
      }
    },
    emitToParentEvent() {
      this.$emit("update", { tab: 'EventInfo' })
    },
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
#main {
  display: flex;
  justify-content: center;
  position: relative;
}

#footer {
  font-family: Arial, Helvetica, sans-serif;
  font-style: oblique;
  position: bottom;
  left: 0;
  bottom: 0;
  width: 100%;
  text-align: center;
  opacity: 0.7;
}

.org {
  cursor: pointer;
}

.container {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: space-evenly;
  background: linear-gradient(to bottom, #e8f0fc, #c1d2ee, #a5c5e5, #9abfe2, #90bae0);
  min-height: 100vh;
  min-width: 100vw;
}

#textDiv p {
  text-align: center;
  margin-bottom: 0px;
}

#textDiv ul {
  margin-top: 0;
  text-align: center;
  padding-left: 0px;
  margin-bottom: 0;
  list-style-type: none;
}

#textDiv ul li {
  text-align: center;
}

h5 {
  padding-top: 10px;
}

.inc {
  padding: 5px;
}

.events {
  cursor: pointer;
}

hr {
  margin-bottom: 0;
}</style>
