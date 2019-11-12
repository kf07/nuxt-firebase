<template>
  <section class="container">
    <input v-model="text" type="text" />
    <button @click="handleClick">追加</button>
    <ul>
      <li v-for="(item, index) in chats" :key="index">
        <p>{{ item.from }}</p>
        <p>{{ item.msg }}</p>
      </li>
    </ul>
  </section>
</template>

<script>
import firebase from '~/plugins/firebase'
const db = firebase.firestore()
export default {
  data() {
    return {
      memo: [],
      text: [],
      chats: []
    }
  },
  mounted() {
    const test = db
      .collection('rooms')
      .doc('roomA')
      .collection('message')

    // test.onSnapshot(function(doc) {
    //   console.log('Current data:', doc.data())
    // })
    test.onSnapshot(snapshot => {
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
      db.collection('memo').add({
        id: 1,
        title: this.text
      })
    }
  }
}
</script>

<style>
.container {
  margin: 0 auto;
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  text-align: center;
}

.title {
  font-family: 'Quicksand', 'Source Sans Pro', -apple-system, BlinkMacSystemFont,
    'Segoe UI', Roboto, 'Helvetica Neue', Arial, sans-serif;
  display: block;
  font-weight: 300;
  font-size: 100px;
  color: #35495e;
  letter-spacing: 1px;
}

.subtitle {
  font-weight: 300;
  font-size: 42px;
  color: #526488;
  word-spacing: 5px;
  padding-bottom: 15px;
}

.links {
  padding-top: 15px;
}
</style>
