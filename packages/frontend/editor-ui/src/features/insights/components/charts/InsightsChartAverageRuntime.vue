<script lang="ts" setup>
import { useI18n } from '@n8n/i18n';
import {
	generateLineChartOptions,
	generateLinearGradient,
} from '@/features/insights/chartjs.utils';
import type { ChartData } from 'chart.js';
import { computed, onMounted } from 'vue';
import { Line } from 'vue-chartjs';
import type { ChartProps } from './insightChartProps';
import { ensureChartRegistered } from '@/plugins/chartjs';

const props = defineProps<ChartProps>();

const i18n = useI18n();

onMounted(() => {
	void ensureChartRegistered();
});

const chartOptions = computed(() => generateLineChartOptions());

const chartData = computed<ChartData<'line'>>(() => {
	const labels: string[] = [];
	const runtimeData: number[] = [];

	for (const entry of props.data) {
		labels.push(entry.date);
		runtimeData.push(entry.values.averageRuntimeMs);
	}

	return {
		labels,
		datasets: [
			{
				label: i18n.baseText('insights.chart.averageRuntime'),
				data: runtimeData,
				pointRadius: 0,
				tension: 0.4,
				fill: true,
				backgroundColor: (context) => {
					const chart = context.chart;
					const { ctx, chartArea } = chart;
					if (!chartArea) return '#3E999F';
					return generateLinearGradient(ctx, chartArea.bottom);
				},
				borderColor: '#3E999F',
			},
		],
	};
});
</script>

<template>
	<Line data-test-id="insights-chart-average-runtime" :data="chartData" :options="chartOptions" />
</template>

<style lang="scss" module></style>
