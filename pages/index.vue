<template>
  <div class="container">
    <h1>Big App</h1>
    <br>
    <div class="top">
      <img src="../static/bigup.png" style="width: 100%">
    </div>
    <br>
   <svg ref="svgCard"
    xmlns="http://www.w3.org/2000/svg" 
    viewBox="0 0 227 131.66">
    <rect x="0" y="0" width="227" height="131.66" fill="#fff" stroke="#000000" stroke-width="1"></rect>
    <defs>
      <style>
        .cls-1{font-size:12px;fill:#0d0000;font-family:SignPainter-HouseScript, SignPainter;}
        .cls-2{letter-spacing:-0.16em;}
        .cls-3{fill:none;stroke:#231815;stroke-miterlimit:10;}
        .cls-4{letter-spacing:-0.06em;}
      </style>
    </defs><title>Bigup</title><g id="レイヤー_2" data-name="レイヤー 2">
      <g id="デザイン">
        <text class="cls-1" transform="translate(0.86 10.2)">
          <tspan class="cls-2">T</tspan>
          <tspan x="2.92" y="0">o</tspan>
          {{toSomeone}}
        </text>
        <line class="cls-3" y1="12.66" x2="87" y2="12.66"/>
        <text class="cls-1" transform="translate(140.86 120.2)">
          <tspan class="cls-4">F</tspan>
          <tspan x="3.64" y="0">rom</tspan>
          {{fromSomeone}}
        </text>
        <line class="cls-3" x1="140" y1="122.66" x2="227" y2="122.66"/>
      </g>
    </g>
    <text x="50%" y="30%" font-size="6px" text-anchor="middle">
     Big Up!
    </text>
    <text 
      x="50%" 
      y="50%" 
      font-size="8px"
      text-anchor="middle">{{text1}}
    </text>
   </svg>
    <div style="text-align:right">
      <input v-model="toSomeone" type="text" style="width:50%; margin-bottom:10px">
      <input v-model="fromSomeone" type="text" style="width:50%; margin-bottom:10px">
      <input v-model="text1" type="text" style="width:50%; margin-bottom:10px"><br>
      <button @click="create">create</button>
    </div>
  </div>
</template>

<script>
import firebase from 'firebase'

// Webコンソールから取得したコンフィグをペースト
const config = {
    apiKey: "AIzaSyBg7j8f1oPlcg-C1TMdRA_LK-t5VjMBRBc",
    authDomain: "bakusokuogp-856ef.firebaseapp.com",
    databaseURL: "https://bakusokuogp-856ef.firebaseio.com",
    projectId: "bakusokuogp-856ef",
    storageBucket: "bakusokuogp-856ef.appspot.com",
    messagingSenderId: "34043571975",
    appId: "1:34043571975:web:d959ce1afd99856c845fa2",
    measurementId: "G-RQNCE3DZHF"
  };

// firebase.initializeApp(config);
if (!firebase.apps.length) {
   firebase.initializeApp(config);
}

const db = firebase.firestore()

// svgをpngに変換する関数
function svg2imageData (svgElement, successCallback, errorCallback) {
  var canvas = document.createElement('canvas')
  canvas.width = 1200
  canvas.height = 630
  var ctx = canvas.getContext('2d')
  var image = new Image()
  image.onload = () => {
    ctx.drawImage(image, 0, 0, 1200, 630)
    successCallback(canvas.toDataURL())
  }
  image.onerror = (e) => {
    errorCallback(e)
  }
  var svgData = new XMLSerializer().serializeToString(svgElement)
  image.src = 'data:image/svg+xml;charset=utf-8;base64,' + btoa(unescape(encodeURIComponent(svgData)))
}

function copyToClipboard() {
    // コピー対象をJavaScript上で変数として定義する
    var copyTarget = 'https://firebasestorage.googleapis.com/v0/b/bakusokuogp-856ef.appspot.com/o/.png?alt=media&token=4d1d1877-e78c-4dc0-a14b-c3fa424b5dda';

    // // コピー対象のテキストを選択する
    // copyTarget.select();

    // 選択しているテキストをクリップボードにコピーする
    document.execCommand("Copy");

    // コピーをお知らせする
    alert("クリップボードにコピーしました！ Slackにリンクを貼り付けてBigUpを贈ろう!: " + copyTarget);
}


export default {
  name: 'container',
  data () {
    return {
      toSomeone: 'tsuji-san',
      fromSomeone: 'writor',
      text1: 'いつもありがとう！',
      uuid: '' // 適当に採番する
    }
  },
  methods: {
    create() {
      // refでsvgCardをsvgに設定しているのでthis.$refs.svgCardで要素を取れます
      svg2imageData(this.$refs.svgCard, (data) => {
        const sRef = firebase.storage().ref()
        const fileRef = sRef.child(`${this.uuid}.png`)

        // Cloud Storageにアップロード
        fileRef.putString(data, 'data_url').then((snapshot) => {
          // Firestoreに保存しておく
          const card = db.collection('cards').doc(this.uuid)

          return card.set({
            message: this.description
          }, { merge: false })
        }).then(docRef => {
          console.log(docRef)
        }).catch(err => {
          console.error(err)
        })
      })
      copyToClipboard()
    }
  }
}
</script>

<style>
.container {
  width: 800px;
  height: 800px;
  margin-top: 50px;
  margin-right: auto;
  margin-left: auto;
}
</style>