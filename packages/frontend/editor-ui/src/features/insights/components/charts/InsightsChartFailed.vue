<script lang="ts" setup>
import { useI18n } from '@n8n/i18n';
import { generateBarChartOptions } from '@/features/insights/chartjs.utils';
import { useCssVar } from '@vueuse/core';
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

const colorPrimary = useCssVar('--color-primary', document.body);
const chartOptions = computed(() => generateBarChartOptions());

const chartData = computed<ChartData<'bar'>>(() => {
	const labels: string[] = [];
	const failedData: number[] = [];

	for (const entry of props.data) {
		labels.push(entry.date);
		failedData.push(entry.values.failed);
	}

	return {
		labels,
		datasets: [
			{
				label: i18n.baseText('insights.chart.failed'),
				data: failedData,
				backgroundColor: colorPrimary.value,
			},
		],
	};
});
</script>

<template>
	<Bar data-test-id="insights-chart-failed" :data="chartData" :options="chartOptions" />
</template>

<style lang="scss" module></style>
