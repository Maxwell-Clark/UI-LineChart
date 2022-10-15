<template>
    <div  v-show="loaded == true">
    <div id="parent">
      <div class="dayDetails" v-if='title != ""'>
        <h1>{{title}}</h1>
        <p>${{price}}</p>
        <p>{{post}}</p>
        <a id="siteUrl" :href="siteUrl" target='_blank'>Go To Post</a>
      </div>
    </div>
    

        <LineChartGenerator
            :chart-options="chartOptions"
            :chart-data="chartData"
            :chart-id="chartId"
            :dataset-id-key="datasetIdKey"
            :plugins="plugins"
            :css-classes="cssClasses"
            :styles="styles"
            :width="width"
            :height="height"/>
    </div>

</template>

<script>
import { Line as LineChartGenerator } from "vue-chartjs/legacy";

import {
  Chart as ChartJS,
  Title,
  Tooltip,
  Legend,
  LineElement,
  LinearScale,
  CategoryScale,
  PointElement,
} from "chart.js";

ChartJS.register(
  Title,
  Tooltip,
  Legend,
  LineElement,
  LinearScale,
  CategoryScale,
  PointElement
);

export default {
  name: "LineChart",
  components: {
    LineChartGenerator,
  },
  props: {
    dbData: {
      type: Array,
      default: () => []
    },
    dbLabels: {
      type: Array,
      default: () => []
    },
    dbURLs: {
      type: Array,
      default: () => []
    },
    chartLabels: {
      type: Array,
      default: () => []
    },
    chartId: {
      type: String,
      default: "line-chart",
    },
    datasetIdKey: {
      type: String,
      default: "label",
    },
    width: {
      type: Number,
      default: 400,
    },
    height: {
      type: Number,
      default: 400,
    },
    cssClasses: {
      default: "",
      type: String,
    },
    styles: {
      type: Object,
      default: () => {},
    },
    plugins: {
      type: Array,
      default: () => [],
    }
  },
  data() {
    return {
      documents: [],
      loaded: false,
      siteUrl: "",
      title: "",
      post: "",
      price: "",
      chartData: {
        labels: this.chartLabels,
        datasets: [
          {
            label: "Bitcoin USD",
            backgroundColor: "#f2a900",
            data: this.dbData.map((data) => parseInt(data, 10)),
          }
        ],
      },
      chartOptions: {
        responsive: true,
        maintainAspectRatio: false,
        onHover: (e, item) => this.onClickAction(e, item)
      },
    };
  },
  methods: {
    onClickAction(event, elements) {
      if(elements.length < 1) {
        return
      }
      console.log(event)
      const hoveredIndex = elements[0].index
      this.title = this.chartLabels[hoveredIndex];
      this.post = this.dbLabels[hoveredIndex]
      this.siteUrl = this.dbURLs[hoveredIndex]
      this.price = this.dbData[hoveredIndex]
    }
  },

  async mounted() {
      console.log(this.dbData)
      if(this.dbData.length > 0) {
        this.loaded = true
      }
      console.log("in linechart: " + JSON.stringify(this.chartData.datasets[0].data))
      console.log(this.chartData)
  }
  
};
</script>
<style>
#parent {
  display: flex;
  justify-content: center;
  align-items: center;
  width: 100%;
}

#siteUrl{
  color: white;
  font-weight: bold;
}
.dayDetails{
  width: 80%;
  height: 15em;
  background-color: #f2a900;
  border: 2px solid white;
  border-radius: 6px;
}
</style>
