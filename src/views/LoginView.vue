<template>
  <div class="container">
    <div class="row mt-5">
      <div class="col-md-7 mt-5"> 
      <img src="../assets/undraw_remotely_2j6y.svg" alt="office">
      </div>
      <div class="col-md-5 ">
        <p class="float-start mt-5 mb-5">Sign in</p><br>
        
          <div class=" login col-md-10">
              <form @submit.prevent=" submit" class="ps-2 mt-4"  >
                <div class="form-group first">
                <input v-model="data.username" type="text" class="form-control" placeholder="Email" required id="username">
                </div>
                <div class="form-group last mb-4">
                <input v-model="data.password" type="password" class="form-control" placeholder="Password" required id="password">
                </div>
                
                <input type="submit" value="Log In" class="btn mt-5 float-end login-btn btn-primary">
              </form>
        </div>
        
      </div>
    </div>
  </div>
</template>

<script>
// @ is an alias to /src

import { reactive } from 'vue'
import { useRouter } from 'vue-router'


export default {
  name: 'LoginView',
  
  
  setup() {
    const data = reactive({
      
      username: '',
      password: '',
      clientType: ''
    })
     
    const router = useRouter()
    
    
    const submit =async () => {
      const getDeviceType = () => {
                const ua = navigator.userAgent;
                if (/(tablet|ipad|playbook|silk)|(android(?!.*mobi))/i.test(ua)) {
                    alert("Please use a computer to log in ")
                }
                if (
                    /Mobile|iP(hone|od)|Android|BlackBerry|IEMobile|Kindle|Silk-Accelerated|(hpw|web)OS|Opera M(obi|ini)/.test(
                    ua
                    )
                ) {
                     alert ("Please use a computer to log in ")
                } else {
                    data.clientType = 'web'
                } 
                
      }
            
      getDeviceType()
      
      const response= await fetch('https://gateway-service-5qoj75li4a-uc.a.run.app/api/v1/auth/login', {

        method: 'POST',
        headers: {'Content-Type': 'application/json'},
        body: JSON.stringify(data)
        })
        
        const feedback  = await response.json()
        
        let token = feedback.data.refreshToken
        let cName = "refreshToken"

        try{
          document.cookie = cName + "=" + token

          router.push('/dashboard')
        }catch(
          error
        ){
          console.log(error)
        }
        
    }
    
    return {
      data,
      submit
    }
  }
}
</script>
<style lang="scss">
@import url('https://fonts.googleapis.com/css2?family=Epilogue:wght@300;500;700&display=swap');
.container {

  .row {
    .login-btn {
      margin-right: 3vw;
    }
    .col-md-7 {

      img {
        width: 80%;
      }
    }
    .col-md-5 {
      p{
        padding-top: 20px;
        font-size: 25px;
        font-weight: 500;
        padding-left: 5px;
        color: #848484 !important;

        button {
          border: none;
          background: none;
        }
      }
      #username {
        border: #edf2f5;
        border-bottom-right-radius: 0%;
        border-bottom-left-radius: 0%;
        border-bottom: none;
        width: 90%;
        height: 60px;
        background: #edf2f5;
      }
      #password {
        border: #edf2f5;
        border-top: 1px solid rgb(231, 230, 230);
        border-top-right-radius: 0%;
        border-top-left-radius: 0%;
        width: 90%;
        height: 60px;
        background: #edf2f5;
      }
    }
  }
}
</style>
