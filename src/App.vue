<template>
  <div id="app">
    <img alt="Bitcoin logo" src="./assets/bitcoinlogo.png" />
    <div  v-if="loaded == true"   >
      <LineChart  
        :dbData="chartData"
        :dbLabels="itemLabels"
        :dbURLs="itemURLs"
        :chartLabels="labels"/>
    </div>

  </div>
</template>

<script>
import LineChart from './components/LineChart.vue'
// import lineChartTest from './components/lineChartTest.vue'
import { db } from "./db/db";


export default {
  name: 'App',
  components: {
    LineChart,
    // lineChartTest
  },
  data () {
      return {
        loaded: false,
        chartData: [],
        itemLabels:[],
        itemURLs:[],
        labels: [],
        showError: false,
        errorMessage: 'Please enter a package name'
      }
    },
  methods: {
    resetState () {
        this.loaded = false
        this.showError = false
      },
    async requestData () {
        this.resetState()
        await db.collection('bitcoin')
          .get()
          .then(QuerySnapshot => {
              let docs = QuerySnapshot.docs.map(doc => doc.data())
              console.log("docs " + docs)
            this.documents = docs.sort((a,b) => {
              console.log("sorting")
              let aArr = a.date.split("-");
              let bArr = b.date.split("-");
              if(Number(aArr[1]) > Number(bArr[1])){
                return 1;
              } else if (Number(aArr[1]) < Number(bArr[1])){
                return -1;
              } else {
                if(Number(aArr[0]) > Number(bArr[0])){
                  return 1;
                } else if (Number(aArr[0]) < Number(bArr[0])){
                  return -1;
                } else {
                  return 0;
                }
              }
            })
          })

          if(this.documents.length > 0) {
              this.labels = this.documents.map((obj) => {
                    let newArr = obj.date.split("-");
                    let newDateArr = newArr[1] +"-"+ newArr[0]+"-"+ newArr[2];
                    return newDateArr;
              })
              this.itemLabels = this.documents.map((obj) => obj.redditPostTitle)
              this.chartData = this.documents.map((obj) => obj.currentPrice)
              this.itemURLs = this.documents.map((obj) => obj.redditLink)
              this.loaded = true
          }
      },
  },
  mounted() {
    this.requestData()
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: white ;
  margin-top: 60px;
}
</style>
