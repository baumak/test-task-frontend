<template>
    <div class="about">
        <h5>Distribution for upload #{{upload.id}} user: {{upload.user}}</h5>
        <template v-if="error">
            <div class="text-danger">
                {{error}}
            </div>
        </template>
        <template v-else>
        <div id="chart-basic" >
            <apexchart
                type="bar"
                height="350"
                :options="chartOptionsBasic"
                :series="series"
            ></apexchart>
        </div>
        <div id="chart-radar">
            <apexchart
                type="radar"
                height="350"
                :options="chartOptionsRadial"
                :series="series"
            ></apexchart>
        </div>
        </template>
    </div>
</template>
<script>
import VueApexCharts from "vue-apexcharts";
import { chartOptionsBasic, chartOptionsRadial } from "./data/chart-options";
export default {
    components: {
        apexchart: VueApexCharts,
    },
    name: "Distribution",
    props: {
        msg: String,
    },
    data() {
        return {
            upload: {},
            isLoading: false,
            total: 0,
            distribution: {},
            series: [],
            chartOptionsBasic: chartOptionsBasic,
            chartOptionsRadial: chartOptionsRadial,
            error: null,
        };
    },
    computed: {},
    methods: {
        fetchItems() {
            return this.$axios
                .get(`http://127.0.0.1:5000/uploads/${this.$route.params.id}`)
                .then((data) => {
                    this.upload = data.data;
                    this.generateDistribution();
                })
                .catch(() => {
                    this.upload;
                });
        },

        navigateToDetails(uploadId) {
            this.$router.push(`/uploads/${uploadId}`);
        },
        generateDistribution() {
            try {
                const data = JSON.parse(this.upload?.distibution);
                this.distribution = Object.values(data);
                this.total = this.distribution.reduce(function(a, b) {
                    return a + b;
                });

                const generatedDistribution = {
                    name: "real",
                    data: this.distribution,
                };
                const expectedDistributionData = [
                    0,
                    this.total * 0.301,
                    this.total * 0.176,
                    this.total * 0.125,
                    this.total * 0.097,
                    this.total * 0.079,
                    this.total * 0.067,
                    this.total * 0.058,
                    this.total * 0.051,
                    this.total * 0.046,
                ];
                const expectedDistribution = {
                    name: "expected",
                    data: expectedDistributionData,
                };
                this.series.push(generatedDistribution);
                this.series.push(expectedDistribution);
            } catch (error) {
                this.series = [];
                this.total = 0;
                this.error = {
                    message: "Error generating distribution",
                    error: error.toString(),
                };
            }
        },
    },
    mounted() {
        this.fetchItems();
    },
};
</script>

<style></style>
