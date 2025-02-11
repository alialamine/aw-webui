<script lang="ts">
import _ from 'lodash';
import { ChartOptions } from 'chart.js';
import { Bar } from 'vue-chartjs';
import { Vue, Component, Prop, Watch } from 'vue-property-decorator';
import { get_hour_offset } from '~/util/time';

@Component({
  extends: Bar, // this is important to add the functionality to your component
})
export default class ChartTimelineBars extends Vue<Bar> {
  constructor() {
    super();
  }

  @Prop({
    default: [
      {
        label: 'Total time',
        backgroundColor: '#6699ff',
        data: Array.from({ length: 40 }, () => Math.floor(Math.random() * 40)),
      },
    ],
  })
  datasets: Record<string, any>[];

  @Prop({ default: 'day' })
  resolution: 'day' | 'week';

  mounted() {
    this.renderData();
  }

  @Watch('datasets')
  onDatasetChange() {
    this.renderData();
  }

  renderData() {
    // Overwriting base render method with actual data.
    const hourOffset = get_hour_offset();
    const data = {
      labels: _.range(0, 24).map(h => `${(h + hourOffset) % 24}`),
      datasets: this.datasets,
      title: {
        display: true,
        text: 'Timeline',
      },
      responsive: true,
      maintainAspectRatio: false,
    };
    const options = {
      skipNull: true,
      tooltips: {
        mode: 'point',
        intersect: false,
      },
      legend: {
        display: false,
      },
      scales: {
        xAxes: [{ stacked: true }],
        yAxes: [
          {
            stacked: true,
            ticks: {
              stepSize: 0.25,
              min: 0,
              max: 1,
              callback: function (value: number) {
                if (value == 1) {
                  return '1h';
                } else {
                  return Math.round(value * 60) + 'min';
                }
              },
            },
          },
        ],
      },
    } as ChartOptions;
    this.renderChart(data, options);
  }
}
</script>

<style></style>
