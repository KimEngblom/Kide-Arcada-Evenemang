<template>
    <div v-if="isApiLoaded" class="eventInfo">
        <div>
            <div class="main" v-bind:style="{ color: computedColor, background: computedBackground }">
                <button type="button" class="btn btn-warning btn-floating btn-lg" id="btn-back-to-top" @click="goBack">
                    <!-- <i class="fas fa-arrow-left"></i>Font awesome arrow-->Back
                </button>
                <h1 class="text-center">{{ this.apiData[this.value].name }}</h1>
            </div>
        </div>

        <div class="container">
            <div>
                <!-- Banner -->
                <img class="bannerImg img-fluid mb-3 mt-3"
                    :src="`https://portalvhdsp62n0yt356llm.blob.core.windows.net/bailataan-mediaitems/${this.apiData[this.value].mediaFilename}`" />
            </div>
            <div id="mainContent" class="row justify-content-center">
                <div class="description col-md-8">
                    <p>
                        <!-- Description of event -->
                        <span v-html="eventInfo.description"></span>
                    </p>
                </div>
                <div class="col-md-4">
                    <img
                        :src="`https://api.qrserver.com/v1/create-qr-code/?data=https://kide.app/events/${this.apiData[this.value].id}`" />
                    <br> <br>
                    <a v-bind:style="{ color: computedColor, background: computedBackground }"
                        :href="`https://kide.app/events/${this.apiData[this.value].id}`" type="button"
                        class="btn buttonLink btn-link" target="_blank">To the event on Kide</a> <br> <br>
                    <p
                        v-if="this.apiData[this.value].maxPrice.eur == undefined && this.apiData[this.value].minPrice.eur == undefined">
                        No pricing information available</p>
                    <div v-else>
                        <p>Price for members: {{ this.apiData[this.value].minPrice.eur / 100 }}€ </p>
                        <p>Price for non members: {{ this.apiData[this.value].maxPrice.eur / 100 }}€</p>
                    </div>
                    <p>{{ this.eventInfo.streetAddress }}</p>
                </div>
            </div>
            <footer>
            </footer>
        </div>
    </div>
    <div v-else class="notLoaded">
        <h1>Loading data...</h1>
    </div>
</template>
    
<script>
export default {
    name: 'EventInfo',
    emits: ["update"],
    props: {
        currentTab: String,
        eventId: String,
        value: Number,
        apiData: Array,
        isApiLoaded: Boolean,
        eventInfo: Object,
        color: String,
        background: String,
        boolean: Boolean
    },
    computed: {
        computedColor: function () {
            return this.color;
        },
        computedBackground: function () {
            return this.background;
        }
    },
    methods: {
        goBack() { // Bakåtknappens funktion, ändrar synlig tab
            if (this.boolean) { // Om föregående sida är ForeningInfo, visa den. Annors hoppa till HelloWorld (t.ex om man tryckt på Incoming events evenemanget)
                this.$emit("update", { tab: 'ForeningInfo' })
            } else {
                this.$emit("update", { tab: 'HelloWorld' })
            }
        }
    }
}
</script>

    <!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.main {
    display: block;
    justify-content: center;
    position: relative;
}

.notLoaded h1 {
  font-style: oblique;
  margin: 0;
  padding-top: 115px;
  padding-bottom: 115px;
  font-size: 50px;
  height: 100vh;
  background: linear-gradient(to bottom, #e8f0fc, #c1d2ee, #a5c5e5, #9abfe2, #90bae0);
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

.eventInfo {
    background: linear-gradient(to bottom, #e8f0fc, #c1d2ee, #a5c5e5, #9abfe2, #90bae0);
    min-height: 100vh;
}

.bannerImg {
    width: 550px;
}

.description {
    text-align: left;
    width: 600px;
}

.headerButton {
    left: 20px;
    position: absolute;
}

.buttonLink {
    text-decoration: none;
}

footer {
    height: 80px;
}
</style>