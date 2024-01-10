<template>
  <div>
    <button @click="loginWithLine" v-if="isLoggedIn">Login with LINE</button>
    <div v-else>
      <p>User is logged in with LINE</p>
    </div>
  </div>
</template>

<script setup>
import liff from '@line/liff';
import axios from 'axios';

let isLoggedIn = false;

async function initLiff() {
  await liff
    .init({
      liffId: '2002735361-ZarLbPyG',
    })
    .then(() => {
      if (!liff.isLoggedIn()) {
        liff.login();
      } else {
        isLoggedIn = true;
        console.log('LIFF is logged in');
        sendLineLoginData(); // เรียกใช้งานฟังก์ชันเพื่อเก็บข้อมูล LINE ทันที
      }
    })
    .catch((err) => {
      console.error('LIFF initialization failed', err);
    });
}

initLiff();

async function sendLineLoginData() {
  const lineProfile = await liff.getProfile();
  const lineLoginData = {
    line_user_id: lineProfile.userId,
    display_name: lineProfile.displayName,
    picture_url: lineProfile.pictureUrl || null,
  };

  axios.post('http://localhost:8000/api/line/login', lineLoginData)
    .then(response => {
      console.log(response.data.message);
    })
    .catch(error => {
      console.error('Error sending LINE login data to Laravel API', error);
    });
}

function loginWithLine() {
  liff.login();
}
</script>