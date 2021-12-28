<template>
    <div class="font-khmer_os">
        <div v-if="success" class="flex items-center bg-success text-white text-base font-bold px-4 py-3 mb-3 rounded"
             role="alert">
            <p>គណនីត្រូវបានបង្កើតរួចរាល់</p>
        </div>
        <form class="flex-col text-base font-khmer_os">
            <div class="relative">
                <input
                        type="text"
                        placeholder="នាមត្រកូល"
                        class="p-2 border border-solid border-1 border-light-blue-500 w-full focus:outline-none border-t-0 border-r-0 border-l-0 mb-4"
                        v-model="studentInfo.first_name"
                />
                <span class="absolute r-0 buttom-0 mt-2 text-red-700 text-lg">*</span>
            </div>
            <div class="relative">
                <input
                        type="text"
                        placeholder="នាមខ្លួន"
                        class="p-2 border border-solid border-1 border-light-blue-500 w-full focus:outline-none border-t-0 border-r-0 border-l-0 mb-4"
                        v-model="studentInfo.last_name"
                />
                <span class="absolute r-0 buttom-0 mt-2 text-red-700 text-lg">*</span>
            </div>
        </form>
        <div class="flex justify-center items-center text-base font-khmer_os">
            <label class="flex mr-10">
                <input type="radio" class="mr-2" name="gender" value="M" v-model="studentInfo.gender"/>
                <span>ប្រុស</span>
            </label>
            <label>
                <input type="radio" class="mr-2" name="gender" value="F" v-model="studentInfo.gender"/>
                <span>ស្រី</span>
            </label>
        </div>

        <div class="border border-1 border-gray-300 mt-6 relative rounded">
            <div class="absolute t-0 w-full text-center">
                <div style="margin-top:-10px;">
                    <span style="background-color:#fff;padding:0px 5px;">ពត៏មានផ្ទាល់ខ្លួន</span>
                </div>
            </div>
            <form class="flex-col text-base font-khmer_os w-full p-5">
                <div class="relative">
                    <span class="absolute l-0 buttom-0 mt-2 text-base font-medium text-gray-500 ">+855</span>
                    <input type="text" placeholder="លេខទូរស័ព្ទ"
                           v-model="studentInfo.phone"
                           @keypress="isNumber($event)"
                           class="p-2 border border-solid border-1 border-light-blue-500 w-full focus:outline-none border-t-0 border-r-0 border-l-0 mb-4 pl-10"/>
                    <span class="absolute r-0 buttom-0 mt-2 text-red-700 text-lg">*</span>
                </div>
                <div class="relative">
                    <input type="password" placeholder="ពាក្យសម្ងាត់"
                           v-model="studentInfo.password"
                           class="p-2 px-0 border border-solid border-1 border-light-blue-500 w-full focus:outline-none border-t-0 border-r-0 border-l-0 mb-4"/>
                    <span class="absolute r-0 buttom-0 mt-2 text-red-700 text-lg">*</span>
                </div>
                <div class="relative">
                    <input type="password" placeholder="បញ្ចូលពាក្យសម្ងាត់ផ្ទៀងផ្ទាត់"
                           v-model="studentInfo.confirm_password"
                           class="p-2 px-0 border border-solid border-1 border-light-blue-500 w-full focus:outline-none border-t-0 border-r-0 border-l-0 mb-4"/>
                    <span class="absolute r-0 buttom-0 mt-2 text-red-700 text-lg">*</span>
                </div>
            </form>
        </div>
        <div class="flex justify-center items-center mt-4">
            <label class="flex text-base">
                <input type="radio" class="mr-3 cursor-pointer" v-model="studentInfo.accepted_term"
                       :checked="studentInfo.accepted_term"
                       value="1"
                       name="term"
                >
                <div class="flex"><span class="pr-3">ខ្ញុំយល់ព្រមទៅនឹង</span><span class="text-primary cursor-pointer"
                    @click="showTermAndCondition">លក្ខខណ្ឌនៃការប្រើប្រាស់</span>
                </div>
            </label>
        </div>
        <button class="p-3 mt-4 text-center bg-primary text-white rounded w-full text-base outline-none text-base cursor-pointer font-khmer_os"
                @click="createAccount" :disabled="(loadingRegister || success)"
                :class="(loadingRegister || success)?'bg-opacity-70':'hover:bg-opacity-90'">
            បង្កើតគណនីថ្មី
            <Loader v-if="loadingRegister" :size="10"></Loader>
        </button>
        <ErrMessage v-if="err" :message="message" @closeErr="closeErr"></ErrMessage>
        <TermAndCondition v-if="showTerm" @closeTermAndCondition="closeTermAndCondition"></TermAndCondition>
    </div>
</template>

<script>
    import {mapState, mapActions} from "vuex"
    import ErrMessage from "./components/ErrMessage.vue"
    import Loader from "./../../components/Loader"
    import helper from "./../../helper/helper"
    import TermAndCondition from "./components/TermAndCondition.vue"
    import config from "./../../config"
    export default{
        name: "Create",
        components: {
            ErrMessage,
            Loader,
            TermAndCondition
        },
        data(){
            return {
                err: false,
                message: null,
                showTerm: false,
                success: false,
                studentInfo: {
                    first_name: null,
                    last_name: null,
                    phone: null,
                    password: null,
                    confirm_password: null,
                    gender: null,
                    device_id: null,
                    device_name: null,
                    device_os: null,
                    player_id: null,
                    app_version: config.appVersion,
                    accepted_term: false
                }
            }
        },
        computed: {
            ...mapState('auth', ['loadingRegister'])
        },
        methods: {
            ...mapActions('auth', ['register']),

            closeTermAndCondition(){
                this.showTerm = false
            },

            showTermAndCondition(){
                this.showTerm = true
            },

            closeErr(){
                this.err = false
            },

            isNumber(evt){
                return helper.isNumber(evt)
            },

            createAccount(){
                let device_id = helper.deviceId()
                this.studentInfo.device_id = device_id
                let platform = helper.deviceOs()
                this.studentInfo.device_os = platform

                if (!this.studentInfo.first_name) {
                    this.message = "សូមបញ្ចូលនាមត្រកូល"
                    this.err = true
                    return false
                }

                if (!this.studentInfo.last_name) {
                    this.message = "សូមបញ្ចូលនាមខ្លួន"
                    this.err = true
                    return false
                }

                if (!this.studentInfo.gender) {
                    this.message = "សូមជ្រើរើសភេទ"
                    this.err = true
                    return false
                }

                if (!this.studentInfo.phone) {
                    this.message = "សូមបញ្ចូលលេខទូរស័ព្ទ"
                    this.err = true
                    return false
                }

                if (!this.studentInfo.password) {
                    this.message = "សូមបញ្ចូលលេខសម្ងាត់"
                    this.err = true
                    return false
                }

                if (!this.studentInfo.confirm_password) {
                    this.message = "សូមបញ្ចូលលេខសម្ងាត់ផ្ទៀងផ្ទាត់"
                    this.err = true
                    return false
                }


                if (this.studentInfo.confirm_password !== this.studentInfo.password) {
                    this.message = "ពាក្យសម្ងាត់ និងផ្ទៀងផ្ទាត់ពាក្យសម្ងាត់មិនដូចគ្នា"
                    this.err = true
                    return false
                }

                if (!this.studentInfo.accepted_term) {
                    this.message = "សូមធ្វើការត្រួតពិនិត្យលក្ខណ្ឌ"
                    this.err = true
                    return false
                }

                this.register(this.studentInfo).then(response => {
                    if (response.status === 1) {
                        this.message = response.msg
                        this.err = true
                    } else {
                        this.success = true
                        setTimeout(() => {
                            this.$emit("registerSuccess")
                        }, 3000)
                    }
                }).catch(err => {
                    this.message = err
                    this.err = true
                })


            }
        }
    }
</script>