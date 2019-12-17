<template>
  <section class="container">
    <div class="chat">
      <ul class="chat-list">
        <li v-for="(item, index) in chats" :key="index" class="chat-block">
          <p class="name">{{ item.from }}</p>
          <p class="message">{{ item.msg }}</p>
          <p class="date">{{ item.createdAt | timeFormat }}</p>
        </li>
      </ul>
      <div>
        <label>name：<input v-model="name" type="text"/></label>
        <label>message：<input v-model="text" type="text"/></label>
        <button @click="handleClick">送信</button>
      </div>
    </div>
  </section>
</template>

<script>
import dayjs from 'dayjs'
import firebase from '~/plugins/firebase'
const db = firebase.firestore()

const test = db
  .collection('rooms')
  .doc('roomA')
  .collection('message')

export default {
  filters: {
    timeFormat: function(value) {
      if (value !== undefined) {
        const timestamp = value.seconds * 1000
        return dayjs(timestamp).format('MM/DD HH:mm:ss')
      }
    }
  },
  data() {
    return {
      memo: [],
      text: [],
      chats: [],
      name: ''
    }
  },
  mounted() {
    // test.onSnapshot(function(doc) {
    //   console.log('Current data:', doc.data())
    // })
    test.orderBy('createdAt').onSnapshot(snapshot => {
      snapshot.docChanges().forEach(change => {
        if (change.type === 'added') {
          this.chats.push(change.doc.data())
        }
        // 更新
        else if (change.type === 'modified') {
        }
      })
    })
  },
  methods: {
    handleClick() {
      // db.collection('memo')
      //   .doc('memo1')
      //   .onSnapshot(function(doc) {
      //     console.log('correnct data:', doc.data())
      //   })
      test.add({
        from: this.name,
        msg: this.text,
        createdAt: firebase.firestore.FieldValue.serverTimestamp()
      })
      this.text = ''
    }
  }
}
</script>

<style lang="scss">
.container {
  margin: 0 auto;
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
}

ul,
li {
  list-style: none;
  padding: 0;
}

li {
  display: flex;
}

.chat {
  width: 900px;
}

.chat-list {
  margin-bottom: 20px;
}

.chat-block {
  padding: 3px;
  .name {
    margin-right: 20px;
    width: 20%;
  }
  .message {
    margin-right: 20px;
    width: 40%;
  }
}
</style>
