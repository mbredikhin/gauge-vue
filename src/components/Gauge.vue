<template>
  <div :style="{ textAlign: 'center', padding: '2rem', width: '12rem' }">
    <svg :style="{ overflow: 'visible' }" width="9em" :viewBox="`-1 -1 2 1`">
      <defs>
        <linearGradient
          id="Gauge__gradient"
          gradientUnits="userSpaceOnUse"
          x1="-1"
          x2="1"
          y2="0"
        >
          <stop
            v-for="(color, i) in gradientSteps"
            :key="color"
            :stop-color="color"
            :offset="`${i / (gradientSteps.length - 1)}`"
          />
        </linearGradient>
      </defs>
      <path :d="backgroundArc" fill="#dbdbe7" />
      <path :d="filledArc" fill="url(#Gauge__gradient)" />
      <line y1="-1" y2="-0.165" stroke="white" stroke-width="0.0027" />
      <circle
        :cx="markerLocation[0]"
        :cy="markerLocation[1]"
        r="0.2"
        stroke="#2c3e50"
        stroke-width="0.01"
        :fill="colorScale(percent)"
      />
      <path
        d="M0.136364 0.0290102C0.158279 -0.0096701 0.219156 -0.00967009 0.241071 0.0290102C0.297078 0.120023 0.375 0.263367 0.375 0.324801C0.375 0.422639 0.292208 0.5 0.1875 0.5C0.0852272 0.5 -1.8346e-08 0.422639 -9.79274e-09 0.324801C0.00243506 0.263367 0.0803571 0.120023 0.136364 0.0290102ZM0.1875 0.381684C0.221591 0.381684 0.248377 0.356655 0.248377 0.324801C0.248377 0.292947 0.221591 0.267918 0.1875 0.267918C0.153409 0.267918 0.126623 0.292947 0.126623 0.324801C0.126623 0.356655 0.155844 0.381684 0.1875 0.381684Z"
        :transform="`rotate(${angle * (180 / Math.PI)}) translate(-0.2, -0.33)`"
        fill="#6a6a85"
      />
    </svg>
    <div
      :style="{
        marginTop: '0.4em',
        fontSize: '3em',
        lineHeight: '1em',
        fontWeight: '900',
        fontFeatureSettings: `'zero', 'tnum' 1`,
      }"
    >
      {{ format(',')(value) }}
    </div>
    <div
      v-if="!!label"
      :style="{
        color: '#8b8ba7',
        marginTop: '0.6em',
        fontSize: '1.3em',
        lineHeight: '1.3em',
        fontWeight: '700',
      }"
    >
      {{ label }}
    </div>
    <div
      v-if="!!units"
      :style="{
        color: '#8b8ba7',
        lineHeight: '1.3em',
        fontWeight: '300',
      }"
    >
      {{ units }}
    </div>
  </div>
</template>

<script>
const { arc } = require('d3-shape');
const { scaleLinear } = require('d3-scale');
const { format } = require('d3-format');

export default {
  props: {
    value: {
      type: Number,
      default: 50,
    },
    min: {
      type: Number,
      default: 0,
    },
    max: {
      type: Number,
      default: 100,
    },
    label: {
      type: String,
      required: true,
    },
    units: {
      type: String,
      required: true,
    },
  },
  computed: {
    backgroundArc() {
      return arc()
        .innerRadius(0.65)
        .outerRadius(1)
        .startAngle(-Math.PI / 2)
        .endAngle(Math.PI / 2)
        .cornerRadius(1)();
    },
    percentScale() {
      return scaleLinear()
        .domain([this.min, this.max])
        .range([0, 1]);
    },
    percent() {
      return this.percentScale(this.value);
    },
    angleScale() {
      return scaleLinear()
        .domain([0, 1])
        .range([-Math.PI / 2, Math.PI / 2])
        .clamp(true);
    },
    angle() {
      return this.angleScale(this.percent);
    },
    filledArc() {
      return arc()
        .innerRadius(0.65)
        .outerRadius(1)
        .startAngle(-Math.PI / 2)
        .endAngle(this.angle)
        .cornerRadius(1)();
    },
    colorScale() {
      return scaleLinear()
        .domain([0, 1])
        .range(['#dbdbe7', '#4834d4']);
    },
    gradientSteps() {
      return this.colorScale.ticks(10).map((value) => this.colorScale(value));
    },
    markerLocation() {
      return this.getCoordsOnArc(this.angle, 1 - (1 - 0.65) / 2);
    },
  },
  methods: {
    format(val) {
      return format(val);
    },
    getCoordsOnArc(angle, offset = 10) {
      return [
        Math.cos(angle - Math.PI / 2) * offset,
        Math.sin(angle - Math.PI / 2) * offset,
      ];
    },
  },
};
</script>
