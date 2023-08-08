---
layout: home
# Index page
---
<script type="module">
  // Import the functions you need from the SDKs you need
  import { initializeApp } from "https://www.gstatic.com/firebasejs/9.23.0/firebase-app.js";
  import { getAnalytics } from "https://www.gstatic.com/firebasejs/9.23.0/firebase-analytics.js";
  // TODO: Add SDKs for Firebase products that you want to use
  // https://firebase.google.com/docs/web/setup#available-libraries

  // Your web app's Firebase configuration
  // For Firebase JS SDK v7.20.0 and later, measurementId is optional
  const firebaseConfig = {
    apiKey: "AIzaSyALh7FgoKT-m8DtCZPDCKcO8dpco9xWk-Q",
    authDomain: "sauravnagpalwritings.firebaseapp.com",
    projectId: "sauravnagpalwritings",
    storageBucket: "sauravnagpalwritings.appspot.com",
    messagingSenderId: "792784340932",
    appId: "1:792784340932:web:6d56e7ce2376706aa00691",
    measurementId: "G-R65D17FM5E"
  };

  // Initialize Firebase
  const app = initializeApp(firebaseConfig);
  const analytics = getAnalytics(app);
</script>
