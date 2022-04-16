<template>
  <div class="signedOut">
    <main>
      <h1>Welcome to NEAR!</h1>
      <button v-on:click="login">Sign in</button>
    </main>
  </div>
  
</template>

<script>
import { login } from "../utils"

export default {
  name: "SignedOut",

  methods: {
    login: login
  },
}
</script>
<style scoped>
*{
  transition: .3s ease;
}
.signedOut{
  background: linear-gradient(#f07e6e, #84cdfa, #5ad1cd);
  height: 100vh;
}
main{
  display: flex;
    flex-direction: column;
    align-items: center;
}
button{
  width: 150px;
  height: 150px;
  border-radius: 50%;
  border: solid white 10px;
  box-shadow: 0px 0px 50px #444444;
}
button:hover{
  border: solid white 2px;
  box-shadow: 0px 0px 200px #444444;
}
</style>
