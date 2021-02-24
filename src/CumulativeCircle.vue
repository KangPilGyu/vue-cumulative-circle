<template>
  <div class="circle-progress" :style="{width: `${size}px`, height: `${size}px`}">
    <svg :width="size" :height="size" :viewPort="`0 0 ${size} ${size}`" xmlns="http://www.w3.org/2000/svg">
      <circle class="ring" :stroke="ringColor" :r="r" :cx="size/2" :cy="size/2" :stroke-width="strokeWidth" fill="none"></circle>
      <template v-for="(data, index) in circleList">
        <circle :id="`cumulative-circle-${index}`" v-if="data.percentage > 0" class="progress_circle" :stroke="data.color" :r="r" :cx="size/2" :cy="size/2" :stroke-width="strokeWidth" fill="none"
                :stroke-dasharray="dasharray" :stroke-dashoffset="data.dashOffset"
                :transform="`translate(${size/2}, ${size/2}) rotate(${data.rotate ? data.rotate : 0 }) translate(-${size/2}, -${size/2})`"></circle>
      </template>
    </svg>
  </div>
</template>
<script>
export default {
  name: "cumulative-circle",
  data() {
    return {
      circleList: [],
      dataList: [],
      drawQueue: []
    };
  },
  props: {
    value: {
      type: Array,
      validator: data => {
        return true;
      }
    },
    size: {
      type: Number,
      default: 200
    },
    strokeWidth: {
      type: Number,
      default: 8
    },
    ringColor: {
      type: String,
      default: "#f2f2f2"
    },
  },
  computed: {
    r() {
      return this.size / 2 - this.strokeWidth / 2;
    },
    dasharray() {
      return 2 * Math.PI * this.r;
    },
  },
  async mounted() {
    this.dataList = this.value;
    await this.initCircle();
    setInterval(this.drawCircle, 1000);
  },
  methods: {
    dashoffset(percentage) {
      return this.dasharray * (1 - percentage / 100);
    },
    async sleep(ms) {
      return new Promise(resolve => setTimeout(resolve, ms));
    },
    async initCircle() {
      this.circleList = [];

      this.$nextTick(() => {
        let initCircleList = [];
        let cumulativeTotal = 0;
        for (let i = 0; i < this.dataList.length; i++) {
          initCircleList.push({
            color: this.dataList[i].color,
            dashOffset: this.dashoffset(0),
            percentage: this.dataList[i].percentage,
            rotate: cumulativeTotal != 0 ? ((cumulativeTotal) / 100) * 360 : 0
          });
          cumulativeTotal += parseInt(this.dataList[i].percentage);
        }
        this.circleList = initCircleList;

        for (let i = 0; i < this.circleList.length; i++) {
          this.drawQueue.push({
            index: i,
            dashOffset: this.dashoffset(parseInt(this.circleList[i].percentage))
          });
        }
      });
    },
    drawCircle() {
      if (this.drawQueue.length > 0) {
        let value = this.drawQueue.shift();
        let circle = document.getElementById(`cumulative-circle-${value.index}`);
        if (circle) {
          circle.setAttribute('style','stroke-dashoffset:' + value.dashOffset);
        }
      }
    },
  },
  watch: {
    value: {
      handler() {
        this.drawQueue = [];
        this.dataList = this.value;
        this.initCircle();
      },
      deep: true,
    },
  }
};
</script>
<style lang="css" scoped>
.circle-progress {
  position: relative;
  text-align: center;
  display: inline-block;
}
.circle-progress:after {
  content: attr(data-pct);
  font-size:10px;
  width:100%;
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate3d(-50%, -50%, 0);
}
.circle-progress svg {
  transform: rotate(-90deg);
}
.circle-progress .progress_circle {
  stroke-linecap: round;
  transition: stroke-dashoffset 1s linear;
}
</style>
