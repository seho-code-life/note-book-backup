# vue基础复习todolist

vuecli

1. 双向数据绑定

v-model

2. scoped  css样式

```jsx
<template>
  <div id="app">
    <input v-model="value" placeholder="请输入">
    <button v-on:click="handelClick">点击添加</button>
    <ul>
      <li v-on:click="handelItem(i.id)" v-for="i in arr" :key="i.id">
        {{i.name}}
        <button v-on:click="handelDelete(i.id)">删除</button>
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  name: "App",
  data() {
    return {
      value: "",
      arr: [
        {
          id: 1,
          name: "123"
        }
      ]
    };
  },
  methods: {
    handelClick() {
      if (this.value !== "") {
        this.arr.unshift(this.value);
        this.value = "";
      }
    },
    handelItem() {},
    handelDelete(id) {
      console.log(id);
    }
  }
};
</script>

<style scoped>
li {
  list-style: none;
}
</style>
```