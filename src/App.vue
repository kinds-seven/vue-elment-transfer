<template>
  <div id="app">
    <tri-column-transfer filterable :filter-method="filterMethod" filter-placeholder="请输入城市拼音" v-model="list1"
      :list-two.sync="list2" :titles="['列表', '水果', '蔬菜']" :buttonTexts="['reset', '水果', '蔬菜']"
      :data="data" />
  </div>
</template>

<script>
import TriColumnTransfer from './components/TriColumnTransfer.vue'

export default {
  name: 'App',
  components: {
    TriColumnTransfer
  },
  data() {
    const generateData = _ => {
      const data = [];
      const cities = ['芒果', '苹果', '香蕉', '梨子', '白菜', '萝卜', '茄子'];
      const pinyin = ['mangguo', 'pingguo', 'xiangjiao','lizi', 'baicai', 'luobo', 'qiezi'];
      cities.forEach((city, index) => {
        data.push({
          label: city,
          key: index,
          pinyin: pinyin[index]
        });
      });
      return data;
    };
    return {
      data: generateData(),
      list1: [],
      list2: [],
      filterMethod(query, item) {
        return item.pinyin.indexOf(query) > -1;
      }
    };
  }
}
</script>

<style>
#app {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
}
</style>
