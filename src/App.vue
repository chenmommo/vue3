<template>
  <div>
    <button @click="incStateCount">incStateCount: Clicked {{ state.count }} times.</button>
    <button @click="inc">Clicked {{ count }} times.</button>
    <button @click="increment">Count is: {{ newState.count }}, double is: {{ newState.double }}</button>
  </div>
</template>

<script>
import { ref, reactive, computed, watchEffect, watch } from 'vue'

export default {
  setup() { // crated 只执行一次
    const count = ref(0) // ref对象具有.value指向内部值的单个属性
    // 如果state值改变将会导致state中的state.count同步变化
    // const state = reactive({count})

    // 如果想要作为不同的count监听，就必须除去count属性对于count变量的依赖
    const state = reactive({count: 0}) // 这是一个Proxy对象，定义一个对象，包含多个属性
    console.log(state);

    // state.count = 1
    // const otherCount = ref(2)
    // state.count = otherCount // state.count重新赋值，及那个会改变state对于count的引用
    const inc = () => {
      count.value++
    }
    const incStateCount = () => {
      state.count++
    }
    
    // 监听反应对象中返回的某个属性
    watch(() => state.count, (stateCount, prestateCount)=>{
      // console.log(stateCount, '', prestateCount);
    })
    // 直接监听ref值
    watch(count, (nowCount, prevCount)=>{
      // console.log(nowCount, '', prevCount);
    })

    // 使用watch监听多个源
    watch([count, () => state.count], ([nowCount, stateCount], [prevCount, prestateCount]) => {
      console.log(nowCount, prevCount, stateCount, prestateCount);
    })

    // computed函数
    const newState = reactive({
      count: 1,
      double: computed({
        get: () => newState.count * 2,
        set: val => {
          newState.count = val + 2
        }
      })
    })
    newState.double = 1; // 会执行一次newState中double的set
    function increment() {
      newState.count ++ // 改变newState的count，同时引发computed的get
    }

    // watchEffect函数
    const stop = watchEffect(() => {
      console.log(newState.count)
      if (newState.count > 10) {
        stop()
      }
    })
    return {
      count,
      inc,
      state,
      incStateCount,
      newState,
      increment,
    }
  }
}
</script>

<style scoped>
img {
  width: 200px;
}
h1 {
  font-family: Arial, Helvetica, sans-serif;
}
</style>
