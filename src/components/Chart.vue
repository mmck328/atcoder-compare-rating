<script>
import { Scatter, mixins } from "vue-chartjs";
export default {
  mixins: [Scatter, mixins.reactiveData],
  data: () => {
    return {
      options: {
        elements: {
          line: {
            tension: 0,
            fill: false
          },
        },
        scales: {
          xAxes: [{
            type: 'time'
          }]
        },
        responsive: true,
        maintainAspectRatio: false
      }
    };
  },
  props: {
    chartData: {
      type: Object,
      required: true,
    }
  },
  watch: {
    charData: function() {
      this.renderChart();
    }
  }
};
</script>