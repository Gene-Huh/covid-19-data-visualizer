<template>
  <line-chart v-if="loaded" :chartdata="chartdata" :options="options" />
</template>

<script>
import LineChart from "./LineChart.vue";
export default {
  name: "NewCases",
  components: { LineChart },
  data: () => ({
    loaded: false,
    chartdata: null,
    options: null
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
    }
  },

  async mounted() {
    this.loaded = false;
    try {
      fetch("https://api.covid19api.com/live/country/korea-south")
        .then(response => response.json())
        .then(LiveData => {
          const filteredData = LiveData.map(ld => {
            return {
              Active: ld.Active,
              Date: ld.Date
            };
          });

          this.chartdata = {
            labels: filteredData.map(fd => this.convertDate(fd.Date)),
            datasets: [
              {
                label: LiveData[0].Country,
                borderColor: this.getRandomHex(),
                data: filteredData.map(fd => fd.Active),
                fill: false
              }
            ]
          };
          this.options = {
            title: {
              display: true,
              text: "Active Cases by Day"
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
