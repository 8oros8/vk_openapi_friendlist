<script>
import item from '@/components/item.vue'
import { toRaw } from 'vue'

export default {
  components: {item},
  data() {
    return {
      friendList: [],
    }
  },
  computed: {
    defaultList () { // Если потребуется удалять из основного (левого) списка выбранных друзей, будем пользоваться этим свойством для рендера
      return this.friendList.filter(friend => friend.chosen === false)
    },
    chosenList () {
      return this.friendList.filter(friend => friend.chosen === true)
    },
  },
  methods: {
    vkFetchFriends() {
          VK.Api.call(
            'friends.get',
            {
              v: '5.131',
              fields: 'photo_100',
              count: 50,
            }, (r) => {
              this.friendList = []
                r.response.items.forEach(element => {
                  element.chosen = false
                  this.friendList.push(element)
                })
            });
    },
    vkLogin () {
      VK.Auth.login((response) => {
        if (response.status === 'connected') {
          const user = response.session.user;
          this.vkFetchFriends()
        }
        else if (response.status === 'not_authorized') {

        }
        else if (response.status === 'unknown') {

        }
      },
          VK.access.FRIENDS
      )
    },
    startDrag (evt, item) {
      evt.dataTransfer.dropEffect = 'move'
      evt.dataTransfer.effectAllowed = 'move'
      evt.dataTransfer.setData('friendId', item)
    },
    onDrop (evt, state) {
      const friendId = evt.dataTransfer.getData('friendId')
      this.friendList.find(friend => {
        if (friend.id == friendId) {
          friend.chosen = state
        }
      })
    },
    getSelectedFriends() {
      let selectedArray = []
      for (let item of toRaw(this.chosenList)) {
        selectedArray.push(toRaw(item))
      }
      console.log(selectedArray)
    }
  }
}
</script>

<template>
  <div class="flex justify-around p-6 w-full bg-orange-700">
    <div class="flex flex-col items-center w-1/3 p-6 bg-emerald-800 rounded-md border-4 border-sky-500"
         @drop="onDrop($event, false)"
         @dragover.prevent
         @dragenter.prevent>
      <button class="w-44 h-10 bg-orange-400 rounded-md text-lg text-white pointer-events-auto"
              @click="vkLogin">Login</button>
      <div class="flex items-center justify-center m-4 w-44 h-10 bg-yellow-500 rounded-md text-lg text-white">Total: {{friendList.length}}</div>
      <item v-for="item in friendList"
            :friendData="item"
            :startDrag="startDrag">
      </item>
    </div>
    <div class="flex flex-col items-center w-1/3 p-6 bg-indigo-900 rounded-md border-4 border-sky-500"
         @drop="onDrop($event, true)"
         @dragover.prevent
         @dragenter.prevent>
      <button class="w-44 h-10 bg-lime-600 rounded-md text-lg text-white pointer-events-auto"
              @click="getSelectedFriends">Export to console</button>
      <div class="flex items-center justify-center m-4 w-44 h-10 bg-yellow-500 rounded-md text-lg text-white">Chosen: {{chosenList.length}}</div>
      <item v-for="item in chosenList"
            :friendData="item"
            :startDrag="startDrag">
      </item>
    </div>
  </div>
</template>

<style>

</style>
