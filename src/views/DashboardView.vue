
<template>

<!-- Modal -->
<div class="modal fade" id="staticBackdrop" data-bs-backdrop="static" data-bs-keyboard="false" tabindex="-1" aria-labelledby="staticBackdropLabel" aria-hidden="true">
  <div class="modal-dialog modal-dialog-centered">
    <div class="modal-content ">
      <div class="modal-header">
        <h5 class="modal-title" id="staticBackdropLabel">Update Profile</h5>
        <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
      </div>
      <div class="modal-body">
        <form @submit.prevent="updateUser"  >
            <div class="row">
                <div class="col-md-6">
                    <label class="float-start" for="firstname">First Name</label>
                    <input v-model="data.update.firstName" type="text" name="firstname" class="form-control" placeholder="First name" required id="firstNmae">
                </div>
                <div class="col-md-6">
                    <label class="float-start" for="lastname">Last Name</label>
                    <input v-model="data.update.lastName" type="text" name="lastname" class="form-control" placeholder="Last name" required id="lastName">
                </div>
                <div class="col-md-6 mt-3">
                    <label class="float-start" for="birthday">Date of Birth</label>
                    <input  v-model="data.update.dateOfBirth" type="date" name="birthday" class="form-control" placeholder="Date of birth" required id="dateOfBirth">
                </div>
                <div class="col-md-6  mt-3">
                    <label class="float-start" for="file-upload">Upload an avatar</label>
                    <input type="file" ref="file"  name="file-upload" accept="image/*" class="form-control" id="file-upload" required>
                </div>
            </div>
            <div class="mt-3">
                <input type="submit" value="Submit" @click="handleFileUpload" class="btn btn-secondary float-end" >
            </div>
        </form>
      </div>
      
    </div>
  </div>
</div>
   


   <div class=" whole-view">
    <div class="col-md-2 border aside"> 
        <div class="logo ">
            <p class="logo-text">
                MiniDash
            </p>
            
        </div>
        <hr>
        <div class="dashboard">
            <p><span><i class="fa fa-tachometer" aria-hidden="true"> </i></span>Dashboard</p>
        </div>
        <hr>
        <div class="profile">
            <div class="dropdown">
                <p><span><i class="fa fa-user-circle" aria-hidden="true"></i></span>Profile<i class="fa fa-chevron-down" aria-hidden="true"></i></p>
                <div class="dropdown-content">
                    <!-- Button trigger modal -->
                    <a data-bs-toggle="modal" data-bs-target="#staticBackdrop" >Update Profile</a>
                </div>
            </div>
        </div>

        
                
    
    </div>

    <div class="col-md-10 border main-view"> 

        <div class="main-view-nav shadow-lg p-3 mb-5 bg-body bottom">

            <div class="float-end user-data">

                <p >{{userData.firstName + " " + userData.lastName}}</p>
                <span class="avatar-span"><img src="../assets/logo.png" class="avatar" alt="avatar"></span>

            </div>

        </div>

        <div class="three-cards">
            <div class="card first shadow p-3 mb-5 bg-body rounded"><p>Country</p>
                <span><i class="fa fa-map-marker" aria-hidden="true"></i> </span>
                </div>
            <div class="card second shadow p-3 mb-5 bg-body rounded">
                <p>Company</p>
            <span>
                
                <i class="fa fa-building-o" aria-hidden="true"></i>
            </span>
            </div>
            <!-- <div class="card third shadow p-3 mb-5 bg-body rounded">somethingElse
                
            </div> -->
        </div>

        <div class="countries shadow p-3 mb-5 bg-body rounded">
            <div class="card-title ">country</div>
            <div>
                <ul>
                <li>first</li>
            </ul>
            </div>
        </div>
        
    </div>

   </div>

</template>
<script>
import { onMounted, ref } from 'vue'

export default {
    name: "DashboardView",
    setup() {
            const userData = ref("Please log in")
            const countries = ref("You do not have the necesarry permissions to view countries")

            const data = {
                userToken:{},
                update : {
                    firstName: '',
                    lastName: '',
                    dateOfBirth: '',
                    avatar: ''
                },
                sendToServer : {
                    refreshToken : '',
                    clientType : ""
                }
                }




                //! Need to handle this ASAP



            
            function handleFileUpload(){
                let profilePic = document.querySelector('#file-upload')
                let formData = new FormData();
                formData.append('avatar', profilePic);

                console.log(formData.entries())

                if(profilePic.files[0].size > 5000){
                alert('too big')
                }
                else{
                    let url = "../assets/logo.png"
                    let picture = profilePic.files[0].name
                    let fullPath = url + picture
                    
                    data.update.avatar = fullPath

                }
            }

            
            // Functionality for user update
            const updateUser =async () => {

                const feedback = await fetch('https://gateway-service-5qoj75li4a-uc.a.run.app/api/v1/auth/update-profile', {

                    method: 'POST',
                    headers: {
                        'Content-Type': 'application/json',
                        'Authorization': 'Bearer '+ data.userToken

                    },
                    body: JSON.stringify(data.update)
                    })
                const message = await feedback.json()
                console.log(message)
            }
        
            onMounted(async ()=> {
                const cookie = document.cookie
                const parseCookie = str =>
                    str.split(";").map(v=>v.split('=')).reduce((acc,v)=>{
                        acc[decodeURIComponent(v[0].trim())]= decodeURIComponent(v[1].trim());
                        data.sendToServer.refreshToken=acc.refreshToken ;
                },{});

                parseCookie(cookie);



            // Getting Client Type

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
                    data.sendToServer.clientType = 'web'
                } 
                
            }
            getDeviceType()

            // Getting User Data

            const response = await fetch('https://gateway-service-5qoj75li4a-uc.a.run.app/api/v1/auth/refresh-token', {
                method: 'POST',
                headers: {'Content-Type': 'application/json'},
                body: JSON.stringify(data.sendToServer)
            })
            
            const content = await response.json()
            console.log(content.data)
            userData.value=content.data.user
            data.userToken = content.data.token


            // Getting countries

            const feedback= await fetch('https://gateway-service-5qoj75li4a-uc.a.run.app/api/v1/auth/countries', {

            method: 'GET',
            headers: {
                'Content-Type': 'application/json',
                'Authorization': 'Bearer '+ data.userToken
            }
            })
            
            const result = await feedback.json()
            countries.value = result
            console.log(result.data)
            console.log(countries.value.data)

        })
        return {
             data,
             userData,
             countries,
             updateUser,
             handleFileUpload
            }
    },
}
</script>
<style lang="scss" scoped>
@import url('https://fonts.googleapis.com/css2?family=Epilogue:wght@300;500;700&display=swap');


 .whole-view {
    width: 100vw;
    display: flex;
    .aside{
        .logo{
            .logo-text{
                margin: 1vh 5vw;
                font-size: 18px;
                font-weight: 600;
                text-align: left;
                color: #fff;
                padding-top: 6px;
            }
        }
    height: 100vh;
    background :rgb(108,99,255);
    border: none !important;
    hr {
        margin: 10px 10px;
        color: #fff;

    }
    li{
        list-style: none;

    }
    .fa {
            color: #ddd;
            font-size: 20px;
            padding-right: 10px;
        }
    .dashboard {
       text-align: left;
        margin: 15px 30px; 
        color: #f1f1f1;
        font-weight: 500;
    }
    .profile {
        text-align: left;
        margin: 15px 30px;
        color: #f1f1f1;
        font-weight: 500;
            .fa-chevron-down{
                padding-left: 7px;
                font-size: 13px !important;
            }
        
            .dropdown {
                position: relative;
                display: inline-block;
            }

            .dropdown-content {
                display: none;
                position: absolute;
                border-radius: 6px;
                background-color: #f1f1f1;
                min-width: 160px;
                box-shadow: 0px 8px 16px 0px rgba(0,0,0,0.2);
                padding: 7px;
                z-index: 1;
            }

            .dropdown-content a {
                color: black;
                padding: 12px 16px;
                text-decoration: none;
                border-radius: 6px;
                display: block;
            }

            .dropdown-content a:hover {background-color: #ddd;}

            .dropdown:hover .dropdown-content {display: block;}

            .dropdown:hover .dropbtn {background-color: #3e8e41;}
        }
    
 }
 .main-view{
    border: none !important;
    .three-cards {
        display: flex;
        p{
            position: relative;
            right: 80px;
            top: 10px;
            text-transform: uppercase;
            font-weight: 700;
            font-size: 11px;
        }

        span {
            position: absolute;
            right: 20px;
            top: 30px;
            .fa {
                font-size: 40px;
                color: #aeaeae78;
            }
        }

        .card {
            margin: auto;
            width: 35vw;
            height: 14vh;
            padding: 20px;
            border: none;
        }

        .first {
            border-left: 0.25rem solid #6c63ff!important;
            margin-left: 33px;
            color:#6c63ff;
        }
        .second {
                border-left: 0.25rem solid #1cc88a!important;
                color:#1cc88a;
        }
        .third {
            border-left: 0.25rem solid #f6c23e!important;
            color: #f6c23e;
        }

        
    }
    .countries {
        padding: 0 !important;
        .card-title{
            border-bottom: 1px solid rgba(198, 198, 198, 0.481);
            width: 100%;
            padding: 10px 15px;
        }
            width: 45vw;
            margin-left: 33px;
            text-align: left;
            .card-title {
                text-align: left;

            }
            li{
                list-style: decimal;
                padding: 10px 5px;
            }
        }
    .main-view-nav{
    height: 7vh;
    width: 100%;

    .avatar-span{
        background: #d6d6d6;
        width: 40px;
        position: relative;
        top: -9px;
        border-radius: 50px;
        margin-left: 5px;
        overflow: hidden;

        img {
            object-fit: contain;
            position: relative;
            top: 8px;

        }
    }

    
    .user-data{
        display: flex;
        .avatar {

            height: 20px;

       
    }}
 }}
 }
</style>