<script lang="ts" setup>
import { useI18n } from '@n8n/i18n';
import { generateBarChartOptions } from '@/features/insights/chartjs.utils';
import type { ChartData } from 'chart.js';
import { computed, onMounted } from 'vue';
import { Bar } from 'vue-chartjs';
import type { ChartProps } from './insightChartProps';
import { ensureChartRegistered } from '@/plugins/chartjs';

const props = defineProps<ChartProps>();

const i18n = useI18n();

onMounted(() => {
	void ensureChartRegistered();
});

const chartOptions = computed(() =>
	generateBarChartOptions({
		plugins: {
			tooltip: {
				callbacks: {
					label: (context) => {
						const label = context.dataset.label ?? '';
						return `${label} ${Math.round((context.parsed.y as number) * 100)}%`;
					},
				},
			},
		},
	}),
);

const chartData = computed<ChartData<'bar'>>(() => {
	const labels: string[] = [];
	const failedData: number[] = [];
	const succeededData: number[] = [];

	for (const entry of props.data) {
		labels.push(entry.date);
		failedData.push(entry.values.failedRate);
		succeededData.push(entry.values.successRate);
	}

	return {
		labels,
		datasets: [
			{
				label: i18n.baseText('insights.chart.successRate'),
				data: succeededData,
				backgroundColor: '#3E999F',
			},
			{
				label: i18n.baseText('insights.chart.failureRate'),
				data: failedData,
				backgroundColor: '#ff6f5c',
			},
		],
	};
});
</script>

<template>
	<Bar data-test-id="insights-chart-failure-rate" :data="chartData" :options="chartOptions" />
</template>

<style lang="scss" module></style>
