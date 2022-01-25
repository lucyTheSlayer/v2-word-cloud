## v2-word-cloud: a word cloud vue component
inspired by: [vue-wordcloud](https://github.com/feifang/vue-wordcloud)

This project mainly improves the animation behavior when words are changed.

### install
```shell
npm install v2-word-cloud --save
```

### example

```vue

<template>
  <div>
    <v2-word-cloud
        style="width: 100%; height: 400px; border: 2px solid orange"
        :words="words"
        @wordClicked="onWordClicked"
        :font-size="[12, 68]"
    >
    </v2-word-cloud>
  </div>
</template>
<script>
import { V2WordCloud } from 'v2-word-cloud'

export default {
  name: 'Demo',
  components: {
    V2WordCloud
  },
  data() {
    return {
      words: [
        {
          text: '计算机',
          value: 12,
          bordered: true
        },
        {
          text: '丝绸',
          value: 5.08
        },
        {
          text: '两轮车',
          value: 3
        }
      ]
    }
  },
  methods: {
    onWordClicked(w) {
      console.log(w.text)
    }
  }
}
</script>
```
