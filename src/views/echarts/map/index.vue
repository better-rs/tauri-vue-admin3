<script lang="ts"></script>

<script lang="ts" setup>
  import type { Ref } from 'vue';
  import { onMounted, ref } from 'vue';
  import { registerMap } from 'echarts';
  import { useIntervalFn } from '@vueuse/core';
  import { mapData } from './data';
  import type { createEChartsOption } from '@/hooks/web/useECharts';
  import { useECharts } from '@/hooks/web/useECharts';

  const chartRef = ref<HTMLDivElement | null>(null);
  const { setOptions } = useECharts(chartRef as Ref<HTMLDivElement>);

  const mapOption: createEChartsOption = {
    visualMap: [
      {
        min: 0,
        max: 1000,
        left: 'left',
        text: ['高', '低'],
        calculable: true,
        inRange: {
          // prettier-ignore
          color: ['#313695', '#4575b4', '#74add1', '#abd9e9', '#e0f3f8', '#ffffbf', '#fee090', '#fdae61', '#f46d43', '#d73027', '#a50026'],
        },
      },
    ],
    grid: {
      left: '1%',
      right: '10%',
      top: '5%',
      bottom: 0,
      containLabel: true,
    },
    tooltip: {
      trigger: 'item',
      backgroundColor: 'rgba(0, 0, 0, .6)',
      textStyle: {
        color: '#fff',
        fontSize: 12,
      },
    },
    series: [
      {
        id: 'population',
        type: 'map',
        roam: true,
        map: 'china',
        animationDurationUpdate: 1000,
        universalTransition: true,
        data: mapData,
      },
    ],
  };

  mapData.sort(function (a: Recordable, b: Recordable) {
    return a.value - b.value;
  });

  const barOption: echarts.EChartsOption = {
    grid: {
      left: '1%',
      right: '10%',
      top: '5%',
      bottom: 0,
      containLabel: true,
    },
    xAxis: {
      type: 'value',
    },
    yAxis: {
      type: 'category',
      axisLabel: {
        rotate: 30,
      },
      data: mapData.map(function (item: Recordable) {
        return item.name;
      }),
    },
    animationDurationUpdate: 1000,
    series: {
      type: 'bar',
      id: 'population',
      data: mapData.map(function (item: Recordable) {
        return item.value;
      }),
      universalTransition: true,
    },
  };

  let currentOption = mapOption;

  onMounted(async () => {
    const json = (await (await import('./china.json')).default) as any;
    registerMap('china', json);
    setOptions(mapOption);
    useIntervalFn(
      () => {
        run();
      },
      2000,
      { immediate: true },
    );
  });

  function run() {
    currentOption = currentOption === mapOption ? barOption : mapOption;
    setOptions(currentOption, false);
  }
</script>

<template>
  <div class="page-container">
    <div ref="chartRef" class="chartRef"></div>
  </div>
</template>

<style scoped lang="scss">
  .chartRef {
    width: 100%;
    height: 100%;
  }

  .page-container {
    width: 100%;
    height: 100%;
  }
</style>
