<template>
    <div class="authContentView">
      <paystack v-if="isPay"
      :amount="amount"
      :email="receiptEmail"
      :paystackkey="paystackkey"
      :reference="reference"
      :callback="callback"
      :close="close"
      :embed="false"
    >
    </paystack>
      <div class="inputFieldContainer">
        <img src="@/assets/logo.png" class="brandLogo">
            <v-text-field type="email" prepend-inner-icon="mdi-email-outline" class="emailField" v-model="email" variant="outlined" label="Email Address"></v-text-field>
            <v-text-field
            :type="showPassword ? 'text' : 'password'"  prepend-inner-icon="mdi-lock-outline" :append-inner-icon="showPassword ? 'mdi-eye' : 'mdi-eye-outline'"
             @click:append-inner="viewPassword" class="passwordField"  v-model="password" label="Password" variant="outlined"></v-text-field>  
             <label class="forgotTitleLabel">Forgot password?</label>
              <button v-if="isLogActivated" class="login buttonload">
                  <i class="fa fa-circle-o-notch fa-spin"></i> Logging in... 
              </button>
              <button v-else @click.prevent="handleLogin"  v-on:keyup.enter="handleLogin">Log In</button>
              <label class="forgotTitleLabel" @click.prevent="handleNavToSignUp">Don't have an account? <span>Sign Up</span></label>
              <div class="lineContainer">
                <div class="leftLineView"></div>
                <label class="orLabel">OR</label>
                <div class="rightLineView"></div>
              </div>

              <div class="googleButtonContainer" @click.prevent="loginWithGoogle()">
                <img src="@/assets/google.png" class="googleIcon">
                <label class="googleTitleLabel">Continue with Google</label>
              </div>
      </div>
      <!-- <label class="signUpLabel" @click="handleSignUpTapped">Don't have an account? <span class="signUpSpan"> Register here</span></label> -->
    </div>
</template>
<script>
import { BASE_URL, PICKMORE_MERCHANT_KEY, SIDE_BAR_MENU_ITEM_KEY } from '@/config';
import { ref } from 'vue'
import axios from 'axios';
import { googleSdkLoaded } from "vue3-google-login";
import config from '@/config';
import CryptoJS from 'crypto-js'
import { USER_CACHE_KEY, PAYSTACK_KEY, PRICING } from '@/config'
import APIService from '@/APIService';
import paystack from '../components/paystack.vue';


export default {
  props: {
    isInvite: {
      type: Boolean, 
      default: false
    }
  },
  inject: ["cryptojs"],
  components: {
     paystack
  },
  setup() {
    var email = ref("")
    var password = ref("")
    var isLogActivated = ref(false)
    var showPassword = ref(false)
    var googleUser = ref(null)
    var viewPassword = ref(false)
    var subscriptionType = ref('basic')
    var isPay = ref(false)
    var paystackkey = ref(PAYSTACK_KEY)
    var amount = ref(12600)
    var userId = ref('')
    var receiptEmail = ref("receipt@ziinlo.com")
    return { 
      email, password, isLogActivated, showPassword, googleUser, receiptEmail,
      viewPassword, subscriptionType, isPay, paystackkey, amount, userId 
    }
  },
  computed: {
    reference(){
      let text = "";
      let possible = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789";
      for( let i=0; i < 10; i++ )
        text += possible.charAt(Math.floor(Math.random() * possible.length));
      return text;
    }
  },
  mounted() {
    APIService.init()
    let routeParams = this.$route.query
    console.log('query: ', routeParams.subscription)
    this.subscriptionType = _.get(routeParams, 'subscription', false) 
    if (this.subscriptionType !== false) {
      let selectedPrice = _.get(PRICING, this.subscriptionType.toLowerCase(), false)
      console.log('PRICING: ', PRICING, 'selectedPrice: ', selectedPrice)
    }
   
    // TODO: Compute the price from cedis to dollars
  },
  methods: {
      handleNavToSignUp() {
        if (this.isInvite) {
          this.$emit("didTapNavToSignUp")
        } else {
          let path = "/signup"
          this.$router.push(
            {
              path: path, 
                query: {
                subscription: this.subscriptionType
              }
            })
          // this.$router.push({path: "/signup"})
        }
      },
      viewPassword() {
        this.showPassword = !this.showPassword
      },
      async handleLogin() {
         this.isLogActivated = true 
         var params = {
            phoneNumber : this.phoneNumber, 
            password: this.password
          }

        let userInfo = await APIService.login(params)
        if (userInfo.token.length > 0) {
          userInfo.password = ""
          let token = userInfo.token
          this.encryptAndNavigate(userInfo, token)
        }
      },
      handleSignUpTapped() {
        this.$emit('navToRegister', true)
      }, 
     async loginWithGoogle() {
      var host = window.location.host
      googleSdkLoaded(google => {
        google.accounts.oauth2
          .initCodeClient({
            client_id: config.GOOGLE_AUTH_CLIENT_ID,
            scope: "email profile openid",
            redirect_uri: `${host}/auth/callback`,
            callback: response => {
              if (response.code) {
                this.sendCodeToBackend(response.code);
              }
            }
          })
          .requestCode();
      });
    },
    async sendCodeToBackend(code) {
      try {
        const response = await axios.post(
          "https://oauth2.googleapis.com/token",
          {
            code,
            client_id: config.GOOGLE_AUTH_CLIENT_ID,
            client_secret: config.GOOGLE_AUTH_CLEINT_SECRET,
            redirect_uri: "postmessage",
            grant_type: "authorization_code"
          }
        );
        const accessToken = response.data.access_token;
        // Fetch user details using the access token
        const userResponse = await axios.get(
          "https://www.googleapis.com/oauth2/v3/userinfo",
          {
            headers: {
              Authorization: `Bearer ${accessToken}`
            }
          }
        );

        if (userResponse && userResponse.data) {
          // Set the userDetails data property to the userResponse object
          this.googleUser = userResponse.data;
          this.handleAuthGoogleUser()
        } else {
          // Handle the case where userResponse or userResponse.data is undefined
          console.error("Failed to fetch user details.");
        }
      } catch (error) {
        console.error("Token exchange failed:", error.response.data);
      }
    }, 
   async handleAuthGoogleUser() {
      if (this.googleUser == null) { return }
      // if subscriptionType === standard or enterprise then process payment
      let gUser = this.googleUser
      let params = {
        email: gUser.email, 
        fullName: gUser.name, 
        password: gUser.sub, 
        picture: gUser.picture, 
        isEmailVerified: gUser.email_verified,
        isViaGoogle: true,
        id: Date.now()
      }
      this.receiptEmail = gUser.email
      let fullURL = BASE_URL + "auth/googleAuth"
      await axios.post(fullURL, params).then((response) => {
        if (response.data != null) {
          let data = response.data
          if (data.statusCode == 200) {
            let gUserInfo = data.resp 
            gUserInfo.password = ""
            let token = gUserInfo.token
            this.encryptAndNavigate(gUserInfo, token)
            } else {
              alert(data.msg)
            }
          }
        })
    }, 
     encryptAndNavigate(gUserInfo, token) {
      this.userId = gUserInfo.user._id
        let userDataStr = JSON.stringify(gUserInfo)
        let encyrptedUserData = CryptoJS.AES.encrypt(userDataStr, token).toString()
        let cacheData = {
            token: token, 
            user: encyrptedUserData
        }
        console.log("gUserInfo data: ", gUserInfo, 'isValid: ', (gUserInfo.isNewUser && (this.subscriptionType === 'standard' || this.subscriptionType === 'business')), 'subscription: ', this.subscriptionType)
        localStorage.removeItem(USER_CACHE_KEY)
        localStorage.setItem(USER_CACHE_KEY, JSON.stringify(cacheData))
        if (gUserInfo.isNewUser && (this.subscriptionType === 'standard' || this.subscriptionType === 'business')) {
          this.isPay = true 
        } else if (this.isInvite) {
          this.$emit("didLogIn", gUserInfo.user)
        } else {
          this.$router.push({path: "/boards"})
        }
     }, 
     callback: function(response){
        console.log("paystack: ", response)
        this.isPay = false 
        if (response.status === "success" && response.message === "Approved") {
          console.log('transaction succeeded')
          // Update user subscription type before going home
           this.updateSubscription()
        } else {
          this.$router.push({path: "/"})
        }
      },
      close: function(){
        this.isPay = false 
        this.$router.push({path: "/boards"})
    },
     async updateSubscription() {
        let param = {
          userId: this.userId, 
          subscriptionType: this.subscriptionType
        }
        console.log('update params: ', param)
        await APIService.updateSubscription(param)
        this.$router.push({path: "/boards"})
      }
   }
}
</script>
<style scoped>
.googleTitleLabel {
  font-weight: 500;
  font-size: 16px;
  margin-top: auto;
  margin-bottom: auto;
}
.googleIcon {
  margin-left: 10px;
  width: 40px;
  height: 40px;
  object-fit: contain;
  margin-top: auto;
  margin-bottom: auto;
}
.googleButtonContainer {
  display: flex;
  margin-top: 30px;
  margin-right: auto;
  margin-left: auto;
  width: 340px;
  height: 50px;
  border: 1px solid var(--color-light);
  border-radius: var(--border-radius-1);
}
.orLabel {
  width: 30px;
  height: 30px;
  font-weight: 600;
  margin-top: -12px;
  color: var(--color-dark);
  margin-left: 10px;
}
.leftLineView, .rightLineView {
  width: 140px;
  height: 1px;
  background-color: var(--color-light);
  margin-left: 30px;
}

.rightLineView {
  margin-left: 10px;
}

.lineContainer {
  display: flex;
  margin-top: 50px;
}
.brandLogo {
  width: 60px;
  height: 60px;
  margin-top: 30px;
  margin-left: auto;
  margin-right: auto;
}

.authContentView {
  display: flex;
  flex-direction: column;
  background-color: var(--color-background);
  width: 100vw;
  height: 100vh;
}
.signUpLabel {
  display: block;
  margin-top: 130px;
}

.signUpSpan {
  color: var(--color-blue);
  font-weight: 600;
}
.inputFieldContainer button { 
  margin-top: 30px;
  width: 340px;
  height: 50px;
  margin-right: auto;
  margin-left: auto;
  font-weight: 600;
  font-size: 16px;
  color: white;
  background-color: var(--color-bar-dark);
  border: 0px solid transparent;
  border-radius: var(--border-radius-1);
}

.inputFieldContainer {
  display: flex;
  flex-direction: column;
  background-color: white;
  width: 400px;
  height: 650px;
  margin-right: auto;
  margin-left: auto;
  border-radius: var(--border-radius-2);
  margin-top: 50px;
}

.passwordField, .emailField {
  width: 340px;
  max-height: 44px;
  font-weight: 500;
  font-size: 20px;
  margin-right: auto;
  margin-left: auto;
  border: 0px solid var(--color-light-primary) !important;
  border-color: var(--color-light-primary);
}

.emailField {
  margin-top: 54px;
}
.passwordField {
  margin-top: 30px;
}

.forgotTitleLabel {
  font-weight: 600;
  font-size: 14px;
  color: var(--color-dark);
  margin-top: 30px;
  float: left;
  text-align: left;
  margin-left: 34px;
}
.subTitleLabel {
  color: var(--color-light-primary);
  font-weight: 300;
  font-size: 14px;
}

.titleLabel {
  display: block;
  margin-top: 10px;
  font-weight: 500;
  font-size: 18px;
  margin-top: 30px;
}

</style>