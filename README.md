<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Elite Titans - Auth</title>
  <style>
    body {
      font-family: 'Inter', sans-serif;
      background-color: #f0f8ff;
      display: flex;
      justify-content: center;
      align-items: center;
      height: 100vh;
      margin: 0;
    }
    .box {
      background: white;
      padding: 30px;
      border-radius: 12px;
      box-shadow: 0 0 20px rgba(0,0,0,0.1);
      width: 300px;
    }
    h2 { text-align: center; color: #3366cc; }
    input, select, button {
      width: 100%;
      padding: 10px;
      margin: 10px 0;
      border-radius: 8px;
      border: 1px solid #ccc;
    }
    button {
      background-color: #3366cc;
      color: white;
      cursor: pointer;
    }
    button:hover { background-color: #264d99; }
    .switch {
      text-align: center;
      color: #3366cc;
      cursor: pointer;
      font-size: 14px;
    }
  </style>
</head>
<body>

<div class="box">
  <h2 id="title">Sign Up</h2>
  <input type="email" id="email" placeholder="Email" />
  <input type="password" id="password" placeholder="Password" />
  <select id="role">
    <option value="member">Member</option>
    <option value="admin">Admin</option>
  </select>
  <button id="submit">Sign Up</button>
  <div class="switch" onclick="toggle()">Already have an account? Log In</div>
</div>

<script type="module">
  import { initializeApp } from "https://www.gstatic.com/firebasejs/11.3.1/firebase-app.js";
  import { getAuth, createUserWithEmailAndPassword, signInWithEmailAndPassword } from "https://www.gstatic.com/firebasejs/11.3.1/firebase-auth.js";
  import { getDatabase, ref, set, get } from "https://www.gstatic.com/firebasejs/11.3.1/firebase-database.js";

  const firebaseConfig = {
    apiKey: "AIzaSyAs6SuTkauJ_TSsWrcpbiLe0ae_86i90gs",
    authDomain: "elitetitans-f6307.firebaseapp.com",
    databaseURL: "https://elitetitans-f6307-default-rtdb.firebaseio.com",
    projectId: "elitetitans-f6307",
    storageBucket: "elitetitans-f6307.appspot.com",
    messagingSenderId: "448495913827",
    appId: "1:448495913827:web:95d594215781e2c803a156",
    measurementId: "G-CWWJ70LMKK"
  };

  const app = initializeApp(firebaseConfig);
  const auth = getAuth(app);
  const db = getDatabase(app);

  let loginMode = false;

  document.getElementById("submit").addEventListener("click", () => {
    const email = document.getElementById("email").value.trim();
    const password = document.getElementById("password").value;
    const role = document.getElementById("role").value;

    if (!email || !password) {
      alert("Please fill all fields.");
      return;
    }

    if (!loginMode) {
      // SIGNUP
      createUserWithEmailAndPassword(auth, email, password)
        .then((userCred) => {
          const uid = userCred.user.uid;
          return set(ref(db, "users/" + uid), { email, role });
        })
        .then(() => {
          localStorage.setItem("email", email);
          localStorage.setItem("role", role);
          window.location.href = "dashboard.html";
        })
        .catch(err => alert("Signup Error: " + err.message));
    } else {
      // LOGIN
      signInWithEmailAndPassword(auth, email, password)
        .then((userCred) => {
          const uid = userCred.user.uid;
          return get(ref(db, "users/" + uid));
        })
        .then(snapshot => {
          if (snapshot.exists()) {
            const { email, role } = snapshot.val();
            localStorage.setItem("email", email);
            localStorage.setItem("role", role);
            window.location.href = "dashboard.html";
          } else {
            alert("User data not found in DB.");
          }
        })
        .catch(err => alert("Login Error: " + err.message));
    }
  });

  window.toggle = () => {
    loginMode = !loginMode;
    document.getElementById("title").innerText = loginMode ? "Log In" : "Sign Up";
    document.getElementById("submit").innerText = loginMode ? "Log In" : "Sign Up";
    document.getElementById("role").style.display = loginMode ? "none" : "block";
    document.querySelector(".switch").innerText = loginMode
      ? "Don't have an account? Sign Up"
      : "Already have an account? Log In";
  };
</script>

</body>
</html>
