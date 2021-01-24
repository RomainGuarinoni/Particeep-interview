<template>
  <form action="">
    <select name="categories" id="categories" @change="change">
      <option value="All" selected> All categories</option>
      <option v-for="item in categories" :key="item" :value="item">
        {{ item }}
      </option>
    </select>
  </form>
</template>

<script>
export default {
  props: ["categories"],
  methods: {
    change() {
      let cat = document.getElementById("categories").value;
      this.$emit("CHANGE", {
        cat: cat,
      });
    },
  },
};
</script>

<style>
select {
  outline: none;
  width: 200px;
  height: 40px;
  font-size: 20px;
  border-radius: 10px;
  text-align: center;
}
</style>
