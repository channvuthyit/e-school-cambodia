<template>
    <div class="flex justify-center items-center min-h-screen bg-blue-50">
        <div class="flex-col rounded-2xl w-96 p-6 shadow-lg bg-white">
            <img src="/logo.svg" class="m-auto w-32 mb-8"/>
            <div class="font-khmer_os text-base mb-5  p-0 flex justify-center items-center">
                <div class="px-0 cursor-pointer" @click="switchTap('login')">
                    <p :class="tap=='login'?'text-blue-700':''">ចូលគណនី</p>
                </div>
                <div class="flex justify-center items-center w-10">
                    <div class="border -2 border-t-0 border-b-0 border-l-0 border-gray-300 h-5">
                    </div>
                </div>
                <div @click="switchTap('create')" class="cursor-pointer">
                    <p :class="tap=='create'?'text-blue-700':''">បង្កើតគណនីថ្មី</p>
                </div>
            </div>
            <div v-if="tap == 'login'">
                <form class="flex-col text-base font-khmer_os" v-if="tap == 'login'">
                    <div class="relative">
                        <span class="absolute l-0 buttom-0 mt-2 text-base font-medium text-gray-500 ">+855</span>
                        <input type="text" placeholder="លេខទូរស័ព្ទ" v-model="auth.phone" @keypress="isNumber($event)"
                               class="p-2 border border-solid border-1 border-light-blue-500 w-full focus:outline-none border-t-0 border-r-0 border-l-0 mb-4 pl-10"/>
                        <span class="absolute r-0 buttom-0 mt-2 text-red-700 text-lg">*</span>
                    </div>
                    <div class="relative">
                        <input type="password" placeholder="លេខសម្ងាត់" autocomplete="off" v-model="auth.password"
                               v-on:keyup.enter="studentLogin"
                               class="p-2 px-0 border border-solid border-1 border-light-blue-500 w-full focus:outline-none border-t-0 border-r-0 border-l-0 mb-4"/>
                        <span class="absolute r-0 buttom-0 mt-2 text-red-700 text-lg">*</span>
                    </div>
                    <div class="text-right text-blue-700 cursor-pointer mb-4 mt-1" @click="showForgotPassword()">
                        ភ្លេចពាក្យសម្ងាត់
                    </div>
                </form>

                <div class="p-3 text-center flex justify-center items-center text-white rounded-md w-full text-base outline-none text-base cursor-pointer font-khmer_os hover:bg-blue-800"
                     @click="studentLogin" :class="loginLoading?'bg-blue-400':'bg-blue-700'">
                    <div class="pl-2">
                        <span v-if="!loginLoading">ចូលគណនី</span>
                        <span v-else>កុំពុងដំណើការ &nbsp;<Loader :size="10"/></span>
                    </div>
                </div>
            </div>
            <Create v-if="tap == 'create'" @registerSuccess="registerSuccess"></Create>
        </div>

        <template>
            <ForgotPassword v-if="forgotPassword" @cancel="cancel()" v-on:agree="agree($event)"
                            @changePasswordSuccess="changePasswordSuccess"></ForgotPassword>
        </template>
        <template>
            <Message v-if="errorMessage" :message="errorMessage" @closeMessage="closeMessage"></Message>
        </template>
    </div>
</template>

<script>
    import Create from "./Create";
    import ForgotPassword from "./ForgotPassword"
    import Loader from "./../../components/Loader"
    import Message from "./components/Message.vue"
    import helper from "./../../helper/helper"
    import {mapActions, mapState} from "vuex"
    import config from "./../../config"
    import studentProfileData from "./../../data/student"

    export default{
        components: {
            Create,
            ForgotPassword,
            Loader,
            Message
        },
        data(){
            return {
                tap: "login",
                forgotPassword: false,
                loading: false,
                errorMessage: null,
                auth: {
                    phone: null,
                    password: null,
                    device_id: helper.deviceId(),
                    device_os: helper.deviceOs(),
                    app_version: config.appVersion
                }
            }
        },

        computed: {
            ...mapState('auth', ['loginLoading']),
        },
        methods: {
            ...mapActions('auth', ['login', 'getStudentProfile', 'getToken']),

            registerSuccess(){
                this.tap = "login"
            },

            changePasswordSuccess(){
                this.forgotPassword = false
            },

            switchTap(tap){
                this.tap = tap;
            },

            showForgotPassword(){
                this.auth.password = null
                this.forgotPassword = true;
            },

            cancel(){
                this.forgotPassword = false;
            },

            agree(){
                this.forgotPassword = false;
            },

            closeMessage(){
                this.errorMessage = null;
            },

            isNumber(evt){
                return helper.isNumber(evt)
            },

            studentLogin(){
                if (this.loginLoading) {
                    return
                }
                if (!this.auth.phone) {
                    this.errorMessage = "សូមបញ្ចូលលេខទូរស័ព្ទ";
                    return;
                }

                if (!this.auth.password) {
                    this.errorMessage = "សូមបញ្ចូលពាក្យសម្ងាត់";
                    return;
                }

                if (this.auth.phone && this.auth.password) {
                    this.login(this.auth).then(response => {

                        if (response.data.status !== 0) {
                            this.errorMessage = response.data.msg;
                            return;
                        }

                        let data = response.data.data;

                        localStorage.setItem('token', data.token);
                        let stProfile = studentProfileData

                        stProfile._id = data._id
                        stProfile.first_name = data.first_name
                        stProfile.gender = data.gender
                        stProfile.grade_id = data.grade_id ? data.grade_id : ""
                        stProfile.group_type = data.group_type ? data.group_type : ""
                        stProfile.is_login = data.is_login ? data.is_login : ""
                        stProfile.last_name = data.last_name ? data.last_name : ""
                        stProfile.my_cart = data.my_cart ? data.my_cart : ""
                        stProfile.phone = data.phone ? data.phone : ""
                        stProfile.photo = data.photo ? data.photo : ""

                        if (data.province) {
                            stProfile.province = data.province
                        } else {
                            stProfile.province = {
                                _id: "",
                                name: "E-School"
                            }
                        }
                        if (data.school) {
                            stProfile.school = data.school
                        } else {
                            stProfile.school = {
                                _id: "",
                                name: "E-School"
                            }
                        }

                        localStorage.setItem('stProfile', JSON.stringify(stProfile));

                        this.getStudentProfile(stProfile)
                        this.getToken(data.token)
                        this.$router.push({
                            name: "home"
                        })
                    });

                    return;
                }
            }
        }
    }
</script>