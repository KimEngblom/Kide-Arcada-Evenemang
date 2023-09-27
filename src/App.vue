<template>
  <HelloWorld v-if="currentTab === 'HelloWorld' & isLoaded == true" @update="changeTab" @forening="changeOrg" @getEvent="setEvent" :list="list"
    :apiInfo="apiInfo" :apiDataTlk="apiDataTlk" :apiDataAsk="apiDataAsk" :apiDataHosk="apiDataHosk"
    :apiDataKult="apiDataKult" :apiDataHanse="apiDataHanse" :apiDataComm="apiDataComm"/>

  <ForeningInfo v-else-if="currentTab === 'ForeningInfo'" @update="changeTab" @getEvent="setEvent" :amount="amount"
    :apiData="apiData" :forening="forening" :color="color" :background="background" :boolean="boolean"/>

  <EventInfo v-else-if="currentTab === 'EventInfo' & isApiLoaded == true" @update="changeTab" :eventId="eventId"
    :value="value" :apiData="apiData" :eventInfo="eventInfo" :isApiLoaded="isApiLoaded"
    :color="color" :background="background" :boolean="boolean"/>
</template>

<script>
import HelloWorld from './components/HelloWorld.vue'
import ForeningInfo from './components/ForeningInfo.vue'
import EventInfo from './components/EventInfo.vue'
import axios from 'axios'

export default {
  name: 'App',
  components: {
    HelloWorld,
    ForeningInfo,
    EventInfo
  },
  data() {
    return {
      currentTab: "HelloWorld",
      isLoaded: false, // true när getApiData är färdig
      isApiLoaded: false,
      boolean: false,
      amount: Number,
      list: Number,
      forening: String,
      apiData: [], // Array med alla evenemang för en specifik förening
      apiDataAsk: [], // Alla ASK evenemang
      apiDataHosk: [], // Alla HOSK evenemang
      apiDataKult: [], // Alla KULT evenemang
      apiDataHanse: [], // Alla HANSE evenemang
      apiDataComm: [], // Alla COMMEDIA evenemang
      apiDataTlk: [], // Alla TLK evenemang
      apiInfo: [], // Array med alla evenemang 
      eventInfo: [], // Array med en specifik events info (används i EventInfo)
    }
  },
  methods: {
    changeTab({ tab, boolean }) { // Ändrar synlig tab men hjälp av v-if satsen i template
      this.currentTab = tab
      this.boolean = boolean
    },
    setEvent({ eventId, value }) { // Vi får eventId och value från det specifika evenemanget vi klickade på
      this.eventId = eventId
      this.value = Number(value)

      this.getEventData(this.eventId)
    },
    goBack() { // Bakåtknappens funktion, ändrar synlig tab och rubrik samt gömmer/visar bakåtknappen
      if (this.currentTab === "ForeningInfo") {
        this.currentTab = "HelloWorld"
      } else if ((this.currentTab === "EventInfo")) {
        this.currentTab = "ForeningInfo"
      }
    },
    changeOrg(org) { // Bestämmer vilken data som ska användas och visas beroende på vilken logo man klickar på i HelloWorld taben
      this.namn = org
      if (org === "ask") {
        this.forening = "ASK"
        this.apiData = this.apiDataAsk
        this.amount = this.apiData.length
        this.color = 'white'
        this.background = 'purple'

      } else if (org === "tlk") {
        this.forening = "TLK"
        this.apiData = this.apiDataTlk
        this.amount = this.apiData.length
        this.color = 'white'
        this.background = 'rgb(30, 34, 170)'

      } else if (org === "hosk") {
        this.forening = "HOSK"
        this.apiData = this.apiDataHosk
        this.amount = this.apiData.length
        this.color = 'white'
        this.background = 'green'

      } else if (org === "comm") {
        this.forening = "COMMEDIA"
        this.apiData = this.apiDataComm
        this.amount = this.apiData.length
        this.color = '#2c3e50'
        this.background = 'red'

      } else if (org === "hanse") {
        this.forening = "HANSE"
        this.apiData = this.apiDataHanse
        this.amount = this.apiData.length
        this.color = '#2c3e50'
        this.background = 'yellow'

      } else if (org === "kult") {
        this.forening = "KULT"
        this.apiData = this.apiDataKult
        this.amount = this.apiData.length
        this.color = '#2c3e50'
        this.background = 'turquoise'
      }

      if (this.amount > 5) { // Så att de max visas 3 evenemang i ForeningInfo
        this.amount = 5
      }

      for (let i = 0; i < this.apiData.length; i++) { // Snyggar till tiden när evenemangen börjar (this.apiData[i].dateActualFrom) i ForeningsInfo

        //När evenemanget börjar (DD.MM.YYYY)
        let dateStr = this.apiData[i].dateActualFrom
        if (Date.parse(dateStr)) { // Kollar om dateStr går att parsa, om det inte går -> datumet har redan parsat, gör inget åt den (blir Invalid Date annors när datumet parsas flera gånger)
          let beginDate = Date.parse(dateStr);
          this.apiData[i].dateActualFrom = new Date(beginDate).toLocaleString('fi-FI', { year: 'numeric', month: 'numeric', day: 'numeric', hour: '2-digit', minute: '2-digit' }) // Exkluderar sekunderna
        }

        //Tickets format date
        let ticketStr = this.apiData[i].dateSalesFrom
        if (Date.parse(ticketStr)) { // Kollar om ticketStr går att parsa, om det inte går -> datumet har redan parsat, gör inget åt den (blir Invalid Date annors när datumet parsas flera gånger)
          let ticketDate = Date.parse(ticketStr)
          this.apiData[i].dateSalesFrom = new Date(ticketDate).toLocaleString('fi-FI', { year: 'numeric', month: 'numeric', day: 'numeric', hour: '2-digit', minute: '2-digit' }) // Exkluderar sekunderna
        }
      }
    },
    getEventData(id) { // Hämtar data till ett specifikt evenemang (EventInfo) kallas endast då man klickar på en specifik event
      axios
        .get('https://api.kide.app/api/products/' + id) // Använder ID från setEvent för att APIn ska få rätt info
        .then(res => {
          let text = res.data.model.product.description

          // Detta kunde snyggas till men de fungerar :)
          text = text.replace(/"/g, '')
          text = text.replaceAll("{fi:", '')
          text = text.replaceAll("&amp;", '&')
          text = text.replaceAll("}", '')
          text = text.replaceAll("&gt;", '>')
          text = text.replaceAll(",sv:", '')
          text = text.replaceAll("\\n", '')

          let svenska = text.split(",en:")

          this.eventInfo = res.data.model.product
          this.eventInfo.description = svenska[0]

          this.isApiLoaded = true
        })
        .catch(function (error) {
          console.log('Error: API failed to load: ', error)
        })
    },
    getApiData() {
      axios
        .get('https://api.kide.app/api/products?productType=1') // API fetchar alla evenemang
        .then(res => {
          for (let i = 0; i < res.data.model.length; i++) {

            if (res.data.model[i].companyName.toUpperCase() === "ARCADA STUDERANDEKÅR - ASK") { // Filtrerar alla evenemang till bara de vi vill ha
              let length = this.apiDataAsk.length
              let value = length
              this.apiDataAsk.push(res.data.model[i])
              this.apiDataAsk[length].index = value
              this.apiInfo.push(res.data.model[i])

            } else if (res.data.model[i].companyName.toUpperCase() === "HOSK RF") { // .toUpperCase för att ignorera caps misstag (t.ex Hosk rf & Hosk RF båda accepteras)
              let length = this.apiDataHosk.length
              let value = length
              this.apiDataHosk.push(res.data.model[i])
              this.apiDataHosk[length].index = value
              this.apiInfo.push(res.data.model[i])

            } else if (res.data.model[i].companyName.toUpperCase() === "KULT RF") {
              let length = this.apiDataKult.length
              let value = length
              this.apiDataKult.push(res.data.model[i])
              this.apiDataKult[length].index = value
              this.apiInfo.push(res.data.model[i])

            } else if (res.data.model[i].companyName.toUpperCase() === "HANSE SF") {
              let length = this.apiDataHanse.length
              let value = length
              this.apiDataHanse.push(res.data.model[i])
              this.apiDataHanse[length].index = value
              this.apiInfo.push(res.data.model[i])

            } else if (res.data.model[i].companyName.toUpperCase() === "COMMEDIA RF") {
              let length = this.apiDataComm.length
              let value = length
              this.apiDataComm.push(res.data.model[i])
              this.apiDataComm[length].index = value
              this.apiInfo.push(res.data.model[i])

            } else if (res.data.model[i].companyName.toUpperCase() === "TEKNISKA LÄROVERKETS KAMRATFÖRBUND R.F.") {
              let length = this.apiDataTlk.length
              let value = length
              this.apiDataTlk.push(res.data.model[i])
              this.apiDataTlk[length].index = value
              this.apiInfo.push(res.data.model[i])
            }
          }

          for (let i = 0; i < this.apiInfo.length; i++) { // Snyggar till tiden när evenemangen börjar (this.apiData[i].dateActualFrom) i HelloWorld
            //När evenemanget börjar (DD.MM.YYYY)
            let dateStr = this.apiInfo[i].dateActualFrom
            if (Date.parse(dateStr)) {
              let beginDate = Date.parse(this.apiInfo[i].dateActualFrom)
              this.apiInfo[i].dateActualUntil = new Date(beginDate).toLocaleString('fi-FI', { year: 'numeric', month: 'numeric', day: 'numeric' }) // Exkluderar tiden
              // dateActualUntil så att vi inte ändrar på samma objekt som vi använder för tiden i ForeningInfo, temporary fix?
            }
          }

          this.list = this.apiInfo.length // Används i listan "incoming"

          if (this.list > 3) { // Visa max 3 kommande evenemang i "incoming" listan
            this.list = 3
          }

          this.isLoaded = true // Väntar på getApiData före vi laddar in sidan (viktigt, annors så kan sidan laddas in före api datan existerar vilket leder till undefined problem, v-if satsen i template)
          console.log("API loaded successfully")
        })
        .catch(function (error) {
          console.log('Error: API failed to load: ', error)
        })
    },
    refresh() {
      location.reload()
    }
  },
  mounted() {
    this.getApiData()
    // setInterval(this.refresh, 300000) // Refreshar sidan med 5 min mellanrum, nödvändigt?
  }
}
</script>

<style>

  @import url('https://fonts.googleapis.com/css2?family=Montserrat:wght@300;400;500;600;700;900&display=swap');

#app {
  font-family: 'Montserrat', sans-serif, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

h1 {
  font-size: 60px;
  margin: 0;
  text-align: center;
}
</style>
