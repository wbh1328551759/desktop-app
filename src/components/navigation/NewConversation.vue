<template>
  <div id="create">
    <div class="bar">
      <font-awesome-icon class="back" icon="arrow-left" @click="$emit('conversation-back')" />
      <h3>{{$t('chat.new_conversation')}}</h3>
    </div>
    <Search id="newConversationSearch" class="nav" @input="onInput" />
    <div class="create" @click="$emit('newGroup')">
      <img src="@/assets/logo.png" class="avatar" />
      <h3>{{$t('group.group_new_title')}}</h3>
    </div>
    <mixin-scrollbar>
      <ul class="list">
        <UserItem
          :keyword="keyword"
          v-for="user in currentFriends"
          :key="user.user_id"
          :user="user"
          @user-click="$emit('user-click',user)"
        />
      </ul>
    </mixin-scrollbar>
  </div>
</template>
<script lang="ts">
import UserItem from '@/components/UserItem.vue'
import Search from '@/components/Search.vue'
import accountApi from '@/api/account'

import { Vue, Component } from 'vue-property-decorator'
import { Getter, Action } from 'vuex-class'

@Component({
  components: {
    UserItem,
    Search
  }
})
export default class NewConversation extends Vue {
  @Getter('findFriends') friends: any

  @Action('refreshFriends') actionRefreshFriends: any

  keyword: string = ''

  get currentFriends() {
    const { keyword, friends } = this
    return friends.filter((item: any) => {
      if (keyword !== '') {
        return (
          item.full_name.toUpperCase().includes(keyword.toUpperCase()) ||
          item.identity_number.toUpperCase().includes(keyword)
        )
      }
      return true
    })
  }

  onInput(text: string) {
    this.keyword = text
  }

  mounted() {
    accountApi.getFriends().then(
      (resp: any) => {
        const friends = resp.data.data
        if (friends && friends.length > 0) {
          this.actionRefreshFriends(friends)
        }
      },
      (err: any) => {
        console.log(err)
      }
    )
  }
}
</script>
<style lang="scss" scoped>
#create {
  background: $bg-color;
  display: flex;
  flex-direction: column;
  .list {
    overflow: auto;
    flex: 1 0 0;
  }
}

.bar {
  background: #ffffff;
  display: flex;
  flex-direction: row;
  align-items: center;
  padding: 2.6rem 0 0 0;
  height: 2.5rem;
  width: 100%;
  .back {
    cursor: pointer;
    padding: 0.8rem 0.2rem 0.8rem 1.35rem;
  }
  h3 {
    padding: 0.4rem;
  }
}

.nav {
  border-top: 0.05rem solid $border-color;
  border-bottom: 0.05rem solid $border-color;
  padding: 0.35rem 0.6rem;
  display: flex;
  align-items: center;
  background: $hover-bg-color;
}

.create {
  background: white;
  display: flex;
  flex-direction: row;
  align-items: center;
  padding: 0.2rem 1.1rem;
  cursor: pointer;
  .avatar {
    width: 2.4rem;
    height: 2.4rem;
    margin-right: 0.6rem;
  }
  &:hover,
  &.current {
    background: $hover-bg-color;
  }
  z-index: 10;
  box-shadow: 0 0.05rem 0.05rem #99999933;
}
</style>
