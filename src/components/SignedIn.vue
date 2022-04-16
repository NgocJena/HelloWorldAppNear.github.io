<template>
  <div class="signedIn">
    <button class="link" style="float: right" v-on:click="logout"><img src="@/assets/logout-svgrepo-com.svg" alt="" style="width: 20px;"></button>
    <main>
      <h1 style="margin:0">
        <!-- <label
          for="greeting"
          style="color: var(--secondary);border-bottom: 2px solid var(--secondary);"
        >{{ savedGreeting }}</label> -->
        Hi, "{{ accountId }}"
      </h1>
      <form v-on:submit.prevent="saveGreeting">
        <fieldset ref="fieldset" style="padding-top:20px;">
          <label
            for="greeting"
            style="display:block; color:#ffffff;margin-bottom:0.5em;font-weight:900;"
          >Type your name</label>
          <div class="form-input">
            <input v-model="newGreeting" autocomplete="off" />
            <button id="save" :style="isBtn ? 'background: #0072CE ;' : 'background: #ffbb33'" :disabled="isBtn ? false: true "
            >{{ isBtn ? 'Save' : '	&#8226;	&#8226;	&#8226;'}}</button>
          </div>
        </fieldset>
      </form>
      
      <div v-if="isShowGreeting" class="greeting">
        <!-- <img style="width: 30px; height: 30px;" src="@/assets/loudspeaker-svgrepo-com.svg" alt=""> -->
        <span style="font-size:50px;color: rgb(30 30 30);word-break: break-all;">Hello <b style="color: #ffffff;word-break: break-all">{{ savedGreeting }}</b> !</span>
      </div>
    </main>
    <Notification
      v-show="notificationVisible"
      ref="notification"
      :networkId="networkId"
      :msg="'called method: set_greeting'"
      :contractId="contractId"
      :visible="false"
    />
  </div>
</template>

<script>
import { logout } from "../utils"

import Notification from "./Notification.vue"

export default {
  name: "SignedIn",

  // beforeMount() {
  //   if (this.isSignedIn) {
  //     this.retrieveSavedGreeting()
  //   }
  // },

  components: {
    Notification,
  },

  data: function () {
    return {
      savedGreeting: "",
      newGreeting: "",
      isShowGreeting: false,
      isBtn: false,
      notificationVisible: false,
    }
  },

  computed: {
    // isSignedIn() {
    //   return window.walletConnection? window.walletConnection.isSignedIn(): false
    // },
    accountId() {
      return window.accountId
    },
    contractId() {
      return window.contract? window.contract.contractId: null
    },
    networkId() {
      return window.networkId
    },
  },
  watch: {
    savedGreeting(val){
      if(val.length > 0){
        this.isShowGreeting = true;
      }else{ 
        this.isShowGreeting = false;
      }
    },
    newGreeting(val){
      if(val.length > 0){
        this.isBtn = true;
      }else{ 
        this.isBtn = false;
      }
    }
  },
  methods: {
    retrieveSavedGreeting() {
      //retrieve greeting
      window.contract
        .get_greeting({ account_id: window.accountId })
        .then((greetingFromContract) => {
          this.savedGreeting = greetingFromContract
          this.newGreeting = greetingFromContract
        })
    },

    saveGreeting: async function () {
      // fired on form submit button used to update the greeting

      // disable the form while the value gets updated on-chain
      this.$refs.fieldset.disabled = true

      try {
        
        // make an update call to the smart contract
        await window.contract.set_greeting({
          // pass the new greeting
          message: this.newGreeting,
        })
      } catch (e) {
        alert(
          "Something went wrong! " +
            "Maybe you need to sign out and back in? " +
            "Check your browser console for more info."
        )
        throw e //re-throw
      } finally {
        // re-enable the form, whether the call succeeded or failed
        this.$refs.fieldset.disabled = false
      }

      // update savedGreeting with persisted value
      this.savedGreeting = this.newGreeting
      //show new notification
      this.notificationVisible = true 

      // remove Notification again after css animation completes
      // this allows it to be shown again next time the form is submitted
      setTimeout(() => {
        this.notificationVisible = false
      }, 11000)

    },

    logout: logout,
  },
}
</script>
<style scoped>
*{
  transition: .3s ease;
}
.signedIn{
  background: linear-gradient(#f07e6e, #84cdfa, #5ad1cd);
  height: 100vh;
}
.greeting{
  display:flex;
  align-items: center;
  justify-content: center;
  padding: 20px 0;
  box-shadow: 0px 0px 5px #8d8d8dc2;
  border-radius: 10px;
}
.form-input{
   position: relative;
}
input{
   width: 100%;
   border-radius:20px;
   line-height:30px;
   padding-right: 95px;
   background-color: #ffffff;
   color: rgb(0, 0, 0);
   border: 2px solid rgb(0, 0, 0);
}
#save{
   border-radius:20px;
   position:absolute;
   min-width: 45px;
   right: 5px;
   line-height: 1em;
   top:50%;
   transform: translateY(-50%);
}
[disabled] button:after{
  top:6px;
  right:11px;
}
</style>