<template>
  <div class="v2-word-cloud">
    <svg>
      <g>
      </g>
    </svg>
  </div>
</template>

<script>
import * as d3 from 'd3'
import cloud from 'd3-cloud'
import ResizeObserver from 'resize-observer-polyfill'

function throttle(method, context) {
  clearTimeout(method.tid)
  method.tid = setTimeout(function () {
    method.call(context)
  }, 200)
}

export default /*#__PURE__*/{
  name: 'V2WordCloud', // vue component name
  props: {
    words: {
      type: Array,
      default: () => []
    },
    fontSize: {
      type: Array,
      default: () => [12, 60]
    },
    font: {
      type: String,
      default: 'impact'
    },
    coloring: {
      type: String,
      default: 'random'
    },
    colors: {
      type: Array,
    },
    spiral: { //  two built-in spirals: "archimedean" and "rectangular"
      type: String,
      default: 'archimedean'
    },
    wordPadding: {
      type: Number,
      default: 3
    },
    rotate: {
      type: Object,
      default: function () {
        return {
          from: -60,
          to: 60,
          numOfOrientation: 5
        }
      }
    },
    animationDuration: {
      type: Number,
      default: 1000
    }
  },
  data() {
    return {
      layout: null
    }
  },
  mounted() {
    this.start()
    this.ro = new ResizeObserver((entries, observer) => {
      throttle(this.update)
    })
    this.ro.observe(this.$el)
  },
  beforeDestroy() {
    this.ro.unobserve(this.$el)
  },
  methods: {
    getRotation() {
      const {from: x1, to: x2, numOfOrientation: n} = this.rotate
      const multiplier = ((Math.abs(x1) + Math.abs(x2)) / (n - 1)) || 1
      return {a: n, b: (x1 / multiplier), c: multiplier}
    },
    start() {
      const width = this.$el.clientWidth
      const height = this.$el.clientHeight

      const {a, b, c} = this.getRotation()

      this.layout = cloud()
          .size([width, height])
          .padding(this.wordPadding)
          .font(this.font)
          .fontSize(d => this.scaleL(d.value))
          .rotate(() => {
            return (~~(Math.random() * a) + b) * c
          })
          .spiral(this.spiral)
          .on('end', this.draw)
    },
    update() {
      const width = this.$el.clientWidth
      const height = this.$el.clientHeight
      this.layout
          .stop()
          .size([width, height])
          .words(this.words)
          .start()
    },
    draw(words) {
      const width = this.$el.clientWidth
      const height = this.$el.clientHeight
      let texts = d3.select(this.$el)
          .select('svg')
          .attr('width', width)
          .attr('height', height)
          .select('g')
          .attr('transform', `translate(${width / 2},${height / 2})`)
          .selectAll('text')
          .data(words, w => w.text)
      texts.exit()
          .remove()
      texts = texts
          .enter()
          .append('text')
          .text(d => d.text)
          .on('click', (e, d) => {
            this.$emit('wordClicked', d)
          })
          .merge(texts)
      texts.style('font-family', d => d.font)
        .attr('text-anchor', 'middle')
        .style('fill', this.fillFunc)
        .style('outline', (d, i) => d.bordered ? `2px solid ${this.fillFunc(d, i)}` : '')
        .transition()
        .style('font-size', d => {
          return `${d.size}px`
        })
        .attr('transform', d => {
          return `translate(${[d.x, d.y]})rotate(${d.rotate})`
        })
        .duration(this.animationDuration)
    }
  },
  computed: {
    fill() {
      if (this.colors)
        return d3.scaleOrdinal().range(this.colors)
      else
        return d3.scaleOrdinal(d3.schemeCategory10)
    },
    fillFunc() {
      switch (this.coloring) {
        case 'random':
          return (d, i) => this.fill(i)
        case 'size':
          return (d) => this.fill(d.size)
        default:
          return (d, i) => this.fill(i)
      }
    },
    scaleL() {
      return d3.scaleLog()
          .domain(d3.extent(this.words.map(w => w.value)))
          .range(this.fontSize)
    }
  },
  watch: {
    words: {
      handler: function (val, oldVal) {
        this.update()
      },
      deep: true
    }
  }
}
</script>

<style scoped>
.v2-word-cloud {
  border: 1px solid orange;
}
</style>
