<template>
  <div>
    <div class="search">
      <input v-model="keyword" class="search-input" type="text" placeholder="请输入城市名或者拼音">
    </div>
    <div class="search-content"
         ref="search"
         v-show="keyword"
    >
      <!--当keyword有值的时候显示，搜索框，没有值则不显示-->
      <ul>
        <li class="search-item border-bottom"
            v-for="item of list"
            :key="item.id"
            @click="handleCityClick(item.name)"
        >
          {{item.name}}
        </li>
        <!--当得到的数据长度不为0，就显示，没有得到任何结果就不显示此标签-->
        <li class="search-item border-bottom" v-show="hasNodata">
          没有找到匹配数据
        </li>
      </ul>
    </div>
  </div>

</template>
<!------------------------------------------------>

<script>
import Bscroll from 'better-scroll'
import { mapMutations } from 'vuex'
export default{
  name: 'CitySearch',
  props: {
    cities: Object
  },
  data () {
    return {
      keyword: '',
      list: []
    }
  },
  computed: {
    hasNodata () {
      return !this.list.length
    }

  },
  watch: {
    //  监听keyword数据变化，并使用函数节流
    keyword () {
      if (this.timer) {
        clearTimeout(this.timer)
      }
      //  如果没有任何输入，则list为空
      if (!this.keyword) {
        this.list = []
        return
      }
      this.timer = setTimeout(() => {
        const result = []
        for (let i in this.cities) {
          this.cities[i].forEach((value) => {
            if (value.spell.indexOf(this.keyword) > -1 || value.name.indexOf(this.keyword) > -1) {
              result.push(value)
            }
          })
        }
        this.list = result // 将得到的结果，传给list
      }, 100)
    }
  },
  methods: {
    //  handleCityClick (city) {
    //  this.$store.commit('changeCity', city)
    //  this.$router.push('/')
    //  }
    handleCityClick (city) {
      //  this.$store.dispatch('changeCity', city)
      //  this.$store.commit('changeCity', city)
      this.changeCity(city)
      this.$router.push('/')
    },
    ...mapMutations(['changeCity'])
  },
  mounted () {
    this.scroll = new Bscroll(this.$refs.search)
  }
}

</script>
<!------------------------------------------------>

<style lang="stylus" scoped>
  @import '~styles/varibles.styl'
  .search
    height: .72rem
    background-color: $bgColor
    padding: 0 .1rem
    .search-input
      box-sizing: border-box
      width: 100%
      height: .62rem
      padding: 0 .1rem
      line-height: .62rem
      text-align: center
      border-radius: .06rem
      color: #666
  .search-content
    z-index 1
    overflow: hidden
    position: absolute
    top: 1.58rem
    left: 0
    right: 0
    bottom: 0
    background-color: #eee
    .search-item
      line-height: .62rem
      padding-left: .2rem
      color: #666
      background-color: #fff

</style>
