<template>
  <line-chart v-if="loaded" :chartdata="chartdata" :options="options" style="height: 1000px;"/>
</template>

<script>
import LineChart from "./LineChart.vue";
export default {
  name: "NewCases",
  components: { LineChart },
  data: () => ({
    loaded: false,
    chartdata: null,
    options: null,
    labels: []
  }),
  methods: {
    convertDate(datetime) {
      let date = new Date(datetime);
      const month = [
        "Jan",
        "Feb",
        "Mar",
        "Apr",
        "May",
        "Jun",
        "Jul",
        "Aug",
        "Sep",
        "Oct",
        "Nov",
        "Dec"
      ];
      return month[date.getMonth()] + " " + date.getDate();
    },
    getRandomHex: function() {
      return "#" + Math.floor(Math.random() * 16777215).toString(16);
    },
    dateLabelMaker(data) {
      for (let label of data) {
        if (!this.labels.includes(label)) {
          this.labels.push(label);
        }
      }
    }
  },

  async mounted() {
    this.loaded = false;
    try {
      fetch("https://api.covid19api.com/live/country/united-states")
        .then(response => response.json())
        .then(LiveData => {
          const filteredData = LiveData.map(ld => {
            return {
              Province: ld.Province,
              Active: ld.Active,
              Date: ld.Date
            };
          });
          this.dateLabelMaker(filteredData.map(fd => fd.Date));

          let mergedObj = filteredData.reduce((acc, obj) => {
              if (!acc[obj.Province]) {
                acc[obj.Province] = [obj.Active];
              } else {
                acc[obj.Province].push(obj.Active);
              }
              return acc;
            }, {});

            let statesData = [];
            for (let [key,value] of Object.entries(mergedObj)) {
              statesData.push({
                label: key,
                data: value,
                fill: false,
                borderColor: this.getRandomHex()
              })
            };


          this.chartdata = {
            labels: this.labels.map(label => this.convertDate(label)),
            datasets: statesData
          };
          this.options = {
            title: {
              display: true,
              text: "(USA) Active Cases by Territory",
              fontSize: 20
            },
            responsive: true,
            maintainAspectRatio: false
          };
          this.loaded = true;
        });
    } catch (exception) {
      console.error(exception);
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
