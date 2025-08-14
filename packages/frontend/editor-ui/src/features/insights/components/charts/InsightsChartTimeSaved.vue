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
	const timeSavedData: number[] = [];

	for (const entry of props.data) {
		labels.push(entry.date);
		timeSavedData.push(entry.values.timeSavedMs);
	}

	return {
		labels,
		datasets: [
			{
				label: i18n.baseText('insights.chart.timeSaved'),
				data: timeSavedData,
				pointRadius: 0,
				tension: 0.4,
				fill: true,
				backgroundColor: (context) => {
					const chart = context.chart;
					const { ctx, chartArea } = chart;
					if (!chartArea) return '#ff6f5c';
					return generateLinearGradient(ctx, chartArea.bottom);
				},
				borderColor: '#ff6f5c',
			},
		],
	};
});
</script>

<template>
	<Line data-test-id="insights-chart-time-saved" :data="chartData" :options="chartOptions" />
</template>

<style lang="scss" module></style>
