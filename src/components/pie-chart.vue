<!-- PieChart.vue -->
<template>
  <div class='chart'>
    <canvas id="occupancyChart"></canvas>
  </div>
</template>

<script>
import Chart from 'chart.js/auto';

export default {
  props: {
    occupancyPercentage: {
      type: Number,
      required: true,
    },
    chartTitle: {
      type: String,
      default: 'Taux d\'occupation d\'étage', // Updated default title
    },
  },
  data() {
    return {
      chart: null,
    };
  },
  watch: {
    occupancyPercentage(newValue) {
      if (this.chart) {
        this.chart.destroy();
      }
      this.createChart();
    },
  },
  mounted() {
    this.createChart();
  },
  methods: {
    createChart() {
      const ctx = document.getElementById('occupancyChart').getContext('2d');
      this.chart = new Chart(ctx, {
        type: 'pie',
        data: {
          labels: ['Occupé'],
          datasets: [{
            data: [this.occupancyPercentage, 100 - this.occupancyPercentage],
            backgroundColor: ['#b98c03', '#060710'],
            hoverBackgroundColor: ['#f39422', '#060710'],
          }],
        },
        options: {
          responsive: true,
          maintainAspectRatio: false,
          plugins: {
            title: {
              display: true,
              text: this.chartTitle, // Use the chartTitle prop for the title
              font: {
                size: 20,
                weight: 'bold',
                family: 'Arial',
              },
              padding: {
                top: 20,
                bottom: 20,
              },
              align: 'center',
            },
          },
          legend: {
            position: 'center',
          },
        },
      });
    },
  },
};
</script>

<style scoped>
canvas {
  position: absolute;
  padding: 15px 0%;
  padding-bottom: 15px;
  background-color: #f0f0f0;
  backdrop-filter: blur(10px);
  border: 1px solid #ca8a03;
  box-shadow: 0 0 40px rgba(8,7,16,0.6);
  border-radius: 5%;
}

#occupancyChart {
  display: flex;
  height: 500px;
  width: 200px;
  margin-left: 75%;
  top: 10%;
}

</style>