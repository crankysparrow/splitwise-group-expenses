<script setup>
import { useInfoStore } from 'src/stores/userinfo'
import { storeToRefs } from 'pinia'
import { useRouter } from 'vue-router'
import { ref, watch, onMounted } from 'vue'

const infoStore = useInfoStore()
let drawerLeft = ref(true)

const { userData, groups, currentGroup, groupId } = storeToRefs(infoStore)

onMounted(async () => {
   if (!userData.value) {
      let storageData = localStorage.getItem('userData')
      if (storageData) {
         infoStore.userData = JSON.parse(storageData)
      } else {
         await login()
      }
   }

   if (userData.value && !groups.value) {
      let groupsData = localStorage.getItem('groups')
      if (groupsData) {
         infoStore.groups = JSON.parse(groupsData)
      } else {
         await getGroups()
      }
   }

   if (!currentGroup.value) {
      let group = localStorage.getItem('currentGroup')
      let id = localStorage.getItem('groupId')
      if (group && id) {
         // infoStore.currentGroup = JSON.parse(group)
         currentGroup.value = JSON.parse(group)
         groupId.value = id
      }
   }
   // if (!currentGroupId.value) {
   //    let groupId = localStorage.getItem('groupId')
   //    if (groupId) {
   //       infoStore.groupId = groupId
   //    }
   // }
})

const router = useRouter()

async function login() {
   await fetch('/api/splitwise/user')
      .then((res) => res.json())
      .then((data) => {
         userData.value = data.user
         localStorage.setItem('userData', JSON.stringify(data.user))
         getGroups()
      })
      .catch((err) => {
         console.log(err)
      })
}

async function getGroups() {
   console.log('getting groups')
   await fetch('/api/splitwise/groups')
      .then((res) => res.json())
      .then((data) => {
         infoStore.setGroups(data)
         localStorage.setItem('groups', JSON.stringify(groups.value))
      })
}

function setCurrentGroup(i) {
   let group = groups.value[i]
   group.members.forEach((member, i) => {
      member.currentSplit = 0
      // if (member.id == 1530173) {
      //    // michelle
      //    member.currentSplit = 38
      // } else if (member.id == 12048317) {
      //    // jonathan
      //    member.currentSplit = 31
      // } else if (member.id == 32806672) {
      //    // bárbara
      //    member.currentSplit = 31
      // }
   })
   infoStore.currentGroup = group
   infoStore.groupId = group.id
   localStorage.setItem('currentGroup', JSON.stringify(group))
   localStorage.setItem('groupId', group.id)
   router.push('/recent-expenses')
}
</script>

<template>
   <q-layout view="hHh lpR fFf">
      <q-page-container>
         <q-header elevated class="bg-primary">
            <q-toolbar>
               <q-btn flat @click="drawerLeft = !drawerLeft" icon="menu" />
               <q-btn flat to="/"> Splitwise </q-btn>
               <q-space />

               <div v-if="userData">
                  <q-avatar size="md" class="q-mr-sm">
                     <img :src="userData.picture?.small" />
                  </q-avatar>
                  <span class="q-subtitle-1 q-mr-sm">
                     {{ userData.first_name }} {{ userData.last_name }}
                  </span>
                  <q-btn outline class="q-ml-sm">Log Out</q-btn>
               </div>
               <div v-else>
                  <q-btn outline label="Log In" @click="login" />
               </div>
            </q-toolbar>
         </q-header>
         <q-drawer
            side="left"
            :breakpoint="768"
            bordered
            persistent
            v-model="drawerLeft"
            class="bg-grey-3">
            <q-list>
               <q-item v-if="currentGroup" style="margin-top: 20px; margin-bottom: 10px">
                  <q-item-section>
                     <q-item-label overline>Current Group</q-item-label>
                     <q-item-label>{{ currentGroup.name }}</q-item-label>
                  </q-item-section>
                  <q-item-section avatar>
                     <q-avatar>
                        <img :src="currentGroup.avatar?.small" />
                     </q-avatar>
                  </q-item-section>
               </q-item>

               <q-separator inset />

               <q-item clickable to="/recent-expenses" :disable="currentGroup ? null : true">
                  <q-item-section avatar>
                     <q-icon name="timeline" color="green" />
                  </q-item-section>
                  <q-item-section>Recent Group Expenses</q-item-section>
               </q-item>

               <q-item clickable to="/user-expenses" :disable="currentGroup ? null : true">
                  <q-item-section avatar>
                     <q-icon name="timeline" color="green" />
                  </q-item-section>
                  <q-item-section>User Expenses in Group</q-item-section>
               </q-item>
               <q-item clickable to="/new-expense" :disable="currentGroup ? null : true">
                  <q-item-section avatar>
                     <q-icon name="paid" color="green" />
                  </q-item-section>
                  <q-item-section>New Group Expense</q-item-section>
               </q-item>

               <q-separator inset />

               <q-expansion-item
                  icon="group"
                  expand-icon-class="text-green"
                  label="Groups"
                  :default-opened="groups ? false : true"
                  :disable="groups ? null : true">
                  <template #header>
                     <q-item-section avatar>
                        <q-icon name="group" color="green" />
                     </q-item-section>
                     <q-item-section>Groups</q-item-section>
                  </template>
                  <q-list dense padding>
                     <q-item
                        clickable
                        v-for="(group, i) in groups"
                        :key="`group-${i}`"
                        @click="() => setCurrentGroup(i)"
                        :active="currentGroup.id === group.id">
                        <q-item-section>{{ group.name }}</q-item-section>
                     </q-item>
                  </q-list>
               </q-expansion-item>
            </q-list>
         </q-drawer>
         <router-view></router-view>
      </q-page-container>
   </q-layout>
</template>
