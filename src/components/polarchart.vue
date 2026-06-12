<template>
  <div class="skill-orbit">
    <canvas ref="skillChart"></canvas>
    <div class="skill-orbit__center">
      <span class="skill-orbit__score">{{ averageScore }}</span>
      <span class="skill-orbit__label">Skill Core</span>
    </div>
  </div>
</template>

<script>
import { Chart, registerables } from 'chart.js';
import config from '../config.js';

Chart.register(...registerables);

export default {
  name: 'polarChart',
  data() {
    return {
      configdata: config,
      chart: null,
      skills: [],
      skillPoints: [],
    };
  },
  computed: {
    averageScore() {
      if(!this.skillPoints.length){
        return 0;
      }
      const total = this.skillPoints.reduce((sum, item) => sum + item, 0);
      return Math.round(total / this.skillPoints.length);
    },
  },
  mounted() {
    if(import.meta.env.VITE_CONFIG){
      this.configdata = JSON.parse(import.meta.env.VITE_CONFIG);
    }
    this.skills = this.configdata.polarChart.skills;
    this.skillPoints = this.configdata.polarChart.skillPoints;
    this.renderChart();
  },
  beforeUnmount() {
    this.chart?.destroy();
  },
  methods: {
    renderChart() {
      const ctx = this.$refs.skillChart.getContext('2d');
      const gradient = ctx.createRadialGradient(130, 130, 16, 130, 130, 150);
      gradient.addColorStop(0, 'rgba(124, 255, 178, 0.5)');
      gradient.addColorStop(0.45, 'rgba(0, 229, 255, 0.24)');
      gradient.addColorStop(1, 'rgba(255, 255, 255, 0.04)');

      this.chart = new Chart(ctx, {
        type: 'radar',
        data: {
          labels: this.skills,
          datasets: [
            {
              label: 'Skill Points',
              data: this.skillPoints,
              backgroundColor: gradient,
              fill: false,
              borderColor: 'rgba(124, 255, 178, 0.95)',
              borderWidth: 2,
              pointBackgroundColor: [
                '#7CFFB2',
                '#00E5FF',
                '#FFCF5A',
                '#FF7A90',
                '#C9A7FF',
                '#FFFFFF',
                '#65D7FF',
              ],
              pointBorderColor: 'rgba(6, 12, 24, 0.95)',
              pointBorderWidth: 2,
              pointRadius: 4,
              pointHoverRadius: 6,
              tension: 0.36,
            },
          ],
        },
        options: {
          responsive: true,
          maintainAspectRatio: false,
          layout: {
            padding: 18,
          },
          plugins: {
            legend: {
              display: false,
            },
            tooltip: {
              backgroundColor: 'rgba(8, 14, 28, 0.88)',
              titleColor: '#FFFFFF',
              bodyColor: '#DDF7FF',
              borderColor: 'rgba(124, 255, 178, 0.55)',
              borderWidth: 1,
              padding: 10,
              cornerRadius: 8,
              displayColors: false,
              callbacks: {
                label: (context) => `${context.raw} / 100`,
              },
            },
          },
          scales: {
            r: {
              min: 0,
              max: 100,
              beginAtZero: true,
              ticks: {
                display: false,
                stepSize: 20,
              },
              pointLabels: {
                color: 'rgba(255, 255, 255, 0.88)',
                font: {
                  size: 11,
                  weight: 600,
                },
              },
              grid: {
                circular: true,
                color: (context) => context.index % 2 === 0
                  ? 'rgba(221, 247, 255, 0.2)'
                  : 'rgba(221, 247, 255, 0.08)',
                lineWidth: 1,
              },
              angleLines: {
                color: 'rgba(124, 255, 178, 0.16)',
                lineWidth: 1,
              },
            },
          },
          animation: {
            duration: 1500,
            easing: 'easeOutQuart',
          },
        },
      });
    },
  },
};
</script>

<style scoped>
.skill-orbit {
  position: relative;
  width: min(270px, 76vw);
  height: 270px;
  margin: 0 auto;
  border-radius: 50%;
  background:
    radial-gradient(circle at 50% 50%, rgba(124, 255, 178, 0.14), transparent 24%),
    radial-gradient(circle at 50% 50%, rgba(0, 229, 255, 0.1), transparent 52%),
    linear-gradient(145deg, rgba(255, 255, 255, 0.11), rgba(255, 255, 255, 0.02));
  border: 1px solid rgba(221, 247, 255, 0.22);
  box-shadow:
    inset 0 0 36px rgba(0, 229, 255, 0.09),
    0 18px 46px rgba(0, 0, 0, 0.28);
  overflow: hidden;
}

.skill-orbit::before,
.skill-orbit::after {
  content: "";
  position: absolute;
  inset: 14px;
  border-radius: 50%;
  border: 1px solid rgba(221, 247, 255, 0.12);
  pointer-events: none;
}

.skill-orbit::after {
  inset: 32px;
  border-style: dashed;
  border-color: rgba(124, 255, 178, 0.18);
  animation: orbit-spin 16s linear infinite;
}

.skill-orbit canvas {
  position: relative;
  z-index: 1;
}

.skill-orbit__center {
  position: absolute;
  top: 50%;
  left: 50%;
  z-index: 2;
  width: 76px;
  height: 76px;
  transform: translate(-50%, -50%);
  border-radius: 50%;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  background: rgba(6, 12, 24, 0.62);
  border: 1px solid rgba(124, 255, 178, 0.36);
  box-shadow: 0 0 28px rgba(124, 255, 178, 0.16);
  pointer-events: none;
}

.skill-orbit__score {
  color: #FFFFFF;
  font-size: 24px;
  font-weight: 800;
  line-height: 1;
}

.skill-orbit__label {
  margin-top: 5px;
  color: rgba(221, 247, 255, 0.72);
  font-size: 10px;
  line-height: 1;
}

@keyframes orbit-spin {
  from {
    transform: rotate(0deg);
  }
  to {
    transform: rotate(360deg);
  }
}

@media (max-width: 960px) {
  .skill-orbit {
    width: min(230px, 76vw);
    height: 210px;
    border-radius: 24px;
  }

  .skill-orbit::before,
  .skill-orbit::after {
    border-radius: 24px;
  }

  .skill-orbit__center {
    width: 64px;
    height: 64px;
  }

  .skill-orbit__score {
    font-size: 20px;
  }
}
</style>
