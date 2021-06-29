<template>
  <v-container>
    <v-row class="text-center">
      <v-col cols="12" xs="6" sm="6" md="6" lg="6">
        <v-card>
          <v-form>
            <v-col class="" cols="12">
              <v-text-field
                dense
                label="引用元リンク"
                outlined
                v-model="linkToCitation"
              >
              </v-text-field>
              <v-textarea
                label="論破したい記事見出しなど"
                outlined
                v-model="expression"
              >
              </v-textarea>
            </v-col>

            <v-col cols="12" sm="12" md="12">
              <v-text-field
                dense
                label="「誤解」"
                outlined
                v-model="itemLabel1"
              ></v-text-field>
              <v-text-field
                dense
                label="「誤解」の逆"
                outlined
                v-model="itemLabel2"
              ></v-text-field>
            </v-col>

            <v-col cols="12" sm="12" md="12">
              <div class="Subtitle 2 text-left">印象</div>
              <!-- 印象スライダー -->
              <v-slider
                v-model="imaginaryValue1"
                step="1"
                thumb-label="always"
                track-color="secondary"
                ticks
                :max="10"
              >
                <template v-slot:prepend>
                  <v-text-field
                    dense
                    :label="itemLabel1"
                    disabled
                    outlined
                    type="number"
                    v-model="imaginaryValue1"
                  ></v-text-field>
                </template>
                <template v-slot:append>
                  <v-text-field
                    dense
                    :label="itemLabel2"
                    disabled
                    outlined
                    type="number"
                    v-model="imaginaryValue2"
                  ></v-text-field>
                </template>
              </v-slider>
            </v-col>

            <v-col cols="12" sm="12" md="12">
              <!-- 実態スライダー -->
              <div class="Subtitle 2 text-left">実態</div>
              <v-slider
                v-model="actualValue1"
                thumb-label="always"
                track-color="secondary"
                readonly
                :min="0"
                :max="actualTotalValue"
                ticks
              >
                <template v-slot:prepend>
                  <v-text-field
                    dense
                    :label="itemLabel1"
                    outlined
                    type="number"
                    v-model="actualValue1"
                  ></v-text-field>
                </template>
                <template v-slot:append>
                  <v-text-field
                    dense
                    :label="itemLabel2"
                    outlined
                    type="number"
                    v-model="actualValue2"
                  ></v-text-field>
                </template>
              </v-slider>

              <v-text-field
                dense
                label="エビデンス引用元リンク"
                outlined
                v-model="evidenceCitation"
              >
              </v-text-field>
            </v-col>
          </v-form>
        </v-card>
      </v-col>

      <v-col cols="12" xs="6" sm="6" md="6" lg="6">
        <v-card>
          <v-card-text>
            "{{ expression }}"

            <p class="">
              誤解を生み得る表現の引用元：
              {{ linkToCitation }}
            </p>
          </v-card-text>

          <v-col class="" cols="12">
            <!-- 印象グラフ -->
            上記表現から受ける印象
            <line-chart
              class="horizontal-graph"
              :chart-data="chartData1"
              :options="options"
              :height="150"
            ></line-chart>
            <!-- 実態グラフ -->
            実態
            <line-chart
              class="horizontal-graph"
              :chart-data="chartData2"
              :options="options2"
              :height="150"
            ></line-chart>
          </v-col>
          <v-card-text v-show="evidenceCitation">
            <p class="">実態値のエビデンス：{{ evidenceCitation }}</p>
          </v-card-text>
        </v-card>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
import LineChart from "./HorizontalBarChart.vue";
export default {
  name: "JohariExpression",
  components: {
    LineChart,
  },

  data: () => ({
    // 入力データ
    expression: "",
    linkToCitation: "",
    itemLabel1: "",
    itemLabel2: "",

    imaginaryValue1: 5,
    actualValue1: 50,
    actualValue2: 50,
    actualTotalValue: 100,
    // chart 用受け渡しデータ
    chartData1: {},
    chartData2: {},
    // グラフオプション
    options1: {},
    options2: {},
    evidenceCitation: "",
    options: {
      responsive: true,
      maintainAspectRatio: false,
      aspectRatio: 2,
      resizeDelay: 2,
      legend: {
        position: "chartArea",
      },
      datasets: {
        bar: {
          maxBarThickness: 20,
          barThickness: 20,
        },
      },
      scales: {
        xAxes: [
          {
            stacked: true,
            ticks: {
              display: false,
            },
          },
        ],
        yAxes: [
          {
            stacked: true,
            maxBarThickness: 30,
            display: false,
          },
        ],
      },
    },
  }),
  computed: {
    imaginaryValue2() {
      return 10 - this.imaginaryValue1;
    },
  },
  watch: {
    itemLabel1() {
      this.updateChartData();
    },
    itemLabel2() {
      this.updateChartData();
    },
    imaginaryValue1() {
      this.updateChartData();
    },
    imaginaryValue2() {
      this.updateChartData();
    },
    actualValue1() {
      this.updateChartData();
      this.actualTotalValue =
        Number(this.actualValue1) + Number(this.actualValue2);
      this.updateOptions();
    },
    actualValue2() {
      this.updateChartData();
      this.actualTotalValue =
        Number(this.actualValue1) + Number(this.actualValue2);
      this.updateOptions();
    },
  },
  methods: {
    updateChartData: function () {
      this.chartData1 = {
        labels: ["印象"],
        datasets: [
          {
            label: this.itemLabel1,
            backgroundColor: "#3f51b5",
            data: [this.imaginaryValue1],
            borderWidth: 1,
            datalabels: {
              formatter: function (value) {
                return value;
              },
            },
          },
          {
            label: this.itemLabel2,
            backgroundColor: "#f06292",
            data: [this.imaginaryValue2],
            barThickness: 0.5,
          },
        ],
      };
      this.chartData2 = {
        labels: ["実態"],
        datasets: [
          {
            label: this.itemLabel1,
            backgroundColor: "#3f51b5",
            data: [this.actualValue1],
          },
          {
            label: this.itemLabel2,
            backgroundColor: "#f06292",
            data: [this.actualValue2],
          },
        ],
      };
    },
    updateOptions: function () {
      this.options2 = {
        responsive: true,
        maintainAspectRatio: false,
        aspectRatio: 2,
        resizeDelay: 2,
        legend: {
          position: "chartArea",
        },
        datasets: {
          bar: {
            maxBarThickness: 20,
            barThickness: 20,
          },
        },
        scales: {
          xAxes: [
            {
              stacked: true,
              // display: false,
              ticks: {
                display: true,
              },
            },
          ],
          yAxes: [
            {
              stacked: true,
              maxBarThickness: 30,
              display: false,
            },
          ],
        },
      };
    },
  },
  mounted: function () {
    this.updateChartData();
    this.updateOptions();
  },
};
</script>
<style scoped>
</style>