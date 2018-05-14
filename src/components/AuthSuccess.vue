<template>
  <div>
    <h1>Signup succeeded</h1>
    <button @click='logOut'>Log out</button>
    <hr>
    <img :src="photo" style='height: 120px'> <br>
    <p>{{name}}</p>
    <p>{{email}}</p>
    <p>{{userId}}</p>
    <hr>
     <ul>
        <li v-for="item in items">{{ item.name }}
          <button @click="removeItem(item)">&times;</button>
        </li>
      </ul>
      <p v-if="!items.length">No items found! Add one below.</p>
      <label for="item">Add Item</label> <br>
      <input type="text" v-model="item" @keyup.enter="addItem">
  </div>
</template>

<script>
import firebase from 'firebase'
import { db } from '../main'
let itemsRef;

export default {
 data(){
     return {
       photo: '',
       userId: '',
       name: '',
       email: '',
        items: [],
      item: '',
      user: {}
     }
   },
     firebase: {
    items: itemsRef
  },
   created() {
     

     var vm = this

     firebase.auth().onAuthStateChanged(function(user) {
       if (user) {
         vm.user = user;
         vm.name = vm.user.displayName;
         vm.email = vm.user.email;
         vm.photo = vm.user.photoURL;
         vm.userId = vm.user.uid;
          itemsRef = db.ref('items/' + vm.user.uid);

          itemsRef.once('value').then(function(snapshot) {
              var name = (snapshot.val() && snapshot.val().name) || 'Anonymous';
              for (var key in snapshot.val()) {
                  // vm.items.push(snapshot.val()[key]['name']);
                  var element = snapshot.val()[key];
                  element.key = key;
                  vm.items.push(element);
              }
});
          
          // db.collection('items/' + vm.user.uid).get().then((querySnapshot) => {
          //   querySnapshot.forEach((doc) => {
              
          //     this.contacts.push(doc.data().name)
          //   })
          // })
          //  vm.items = itemsRef;
                         }
    });
  },
  methods: {
    logOut() {
      firebase.auth().signOut();
    },
     addItem () {
        itemsRef.push({
          name: this.item
        }).then(() => {
          this.item = ''
        })  
        location.reload()
      },
       removeItem (item) {
        itemsRef.child(item.key).remove()
        location.reload()
      }
  },
    firebase: {

    itemsfb: itemsRef
  },
};
</script>
