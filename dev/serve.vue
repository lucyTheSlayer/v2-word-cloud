<template>
  <div id="app">
    <v2-word-cloud
        style="width: 40vw; height: 400px;"
        :words="words"
        @wordClicked="onWordClicked"
        :animation-duration="2000"
    />
    <button @click="addWord">add</button>
  </div>
</template>

<script>
export default {
  name: 'ServeDev',
  // components: {
  //  V2WordCloud,
  // }
  data() {
    return {
      i: 0,
      words: [
        {
          text: '计算机',
          value: 12
        },
        {
          text: '华为',
          value: 40
        },
        {
          text: '元宇宙',
          value: 20
        },
        {
          text: '丝绸',
          value: 5.08
        },
        {
          text: '两轮车',
          value: 3
        },
        {
          text: '中医药',
          value: 4
        },
        {
          text: '海上风电',
          value: 10
        },
        {
          text: '电解液',
          value: 6
        },
        {
          text: '生物制药',
          value: 12
        },
        {
          text: '虚拟现实',
          value: 16
        },
        {
          text: '鸿蒙',
          value: 3
        },
        {
          text: '数字货币',
          value: 50,
          bordered: true
        },
        {
          text: '集成电路',
          value: 20
        }
      ]
    }
  },
  methods: {
    addWord () {
      this.words.push({
        text: `word${this.i}`,
        value: Math.random() * 60
      })
      this.i++
    },
    onWordClicked (w) {
      console.log(w.text)
    }
  }
}
</script>
