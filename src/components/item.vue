<template>
  <div class="flex justify-between w-full my-4 items-center bg-cyan-500 rounded-md"
       draggable="true"
       @dragstart="startDrag($event, friendData.id)">
    <div>
      <img :src="friendData.photo_100" class="rounded-l-md">
    </div>
    <div class="font-sans text-white text-lg mr-10">
      {{friendData.first_name}} {{friendData.last_name}}
    </div>
  </div>
</template>

<script>
export default {
  name: "item",
  data () {
    return {
    }
  },
  props: {
    friendData: {},
    startDrag: Function,
  },
}
</script>

<style scoped>

</style>