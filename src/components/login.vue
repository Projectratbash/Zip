<template>
    <div v-if="!showMain" id="loginContainer">

    <div id="logoContainer">
        <img src="src/assets/ZIPLogoCropped.png" alt="">
    </div>

    <div v-if="!loginClicked" class="btnContainer" @click="loginClicked = true">
        <button class="loginBtn blue">Log in</button>
        <button class="loginBtn blue">Sign up</button>
    </div>
    <div v-if="loginClicked" class="btnContainer">
        <p id="loginText">Login</p>
        <input class="loginInput" placeholder="Username" type="text">
        <input class="loginInput" placeholder="Password" type="text">
        <button class="loginBtn blue" @click="showMain = true, showEvent()">Log in</button>
    </div>

    </div>
</template>

<script>
    export default {

        props: {

            loginClicked: Boolean,
            showMain: Boolean

        },
        methods: {
            showEvent(){
            this.$emit('showEvent')
        }
    }
}
</script>

<style>
    #loginContainer {
  width: 100vw;
  height: 100vh;
  display: flex;
  align-items: center;
  flex-direction: column;
  background-image: url(src/assets/loginBgImg.png);
  background-repeat: no-repeat;
  background-size: cover;
  background-position-y: bottom;
}

#loginContainer img {
  width: 70%;
  margin: 0 10px 27px 0;
}

#logoContainer {
  display: flex;
  justify-content: center;
  align-items: center;
  width: 300px;
  height: 300px;
  background-color: black;
  border-radius: 50%;
  margin: 10vh 0 10vh 0;
}

#loginText {
  color: white;
  font-weight: 1000;
}

.btnContainer {
  display: flex;
  flex-direction: column;
  ;
  align-items: center;
}

.loginBtn {
  color: white;
  border: none;
  padding: 8px 50px 8px 50px;
  border-radius: 5px;
  margin-bottom: 15px;
}

.loginInput {
  border: rgba(25, 134, 163, 1) solid 2px;
  border-radius: 5px;
  padding: 8px 20px 8px 20px;
  width: 80%;
  margin-bottom: 20px;
}
</style>

// <!-- The login component is currently in App.vue -->
// <!-- This component is not yet linked to App.vue -->