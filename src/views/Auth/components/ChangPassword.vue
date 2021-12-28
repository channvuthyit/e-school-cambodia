<template>
    <div class="bg-white">
        <div>
            <div class="fixed z-10 inset-0 overflow-y-auto">
                <div class="flex items-end justify-center min-h-screen text-center sm:block sm:p-0">
                    <div class="fixed inset-0 transition-opacity" aria-hidden="true">
                        <div class="absolute inset-0 bg-gray-500 opacity-75"></div>
                    </div>
                    <span class="hidden sm:inline-block sm:align-middle sm:h-screen" aria-hidden="true">&#8203;</span>
                    <div class="inline-block align-bottom bg-white rounded overflow-hidden shadow-xl transform transition-all  sm:align-middle"
                         :class="`w-${size}`" role="dialog" aria-modal="true" aria-labelledby="modal-headline">
                        <div class="bg-white p-5 relative">
                            <div class="absolute right-0 top-0 mr-3 mt-3 cursor-pointer opacity-60"
                                 @click="closeChangePassword">
                                <CloseIcon/>
                            </div>
                            <div class="flex-cols justify-center items-center font-khmer_os text-sm">
                                <div class="mt-2 py-3">
                                    <div class="flex justify-center items-center mb-5">
                                        <div class="bg-gray-300 rounded-full p-2 flex justify-center items-center">
                                            <UserProfileIcon :width="70" :height="70"/>
                                        </div>
                                    </div>
                                    <input type="password"
                                           class="w-full border border-gray-200 border-t-0 border-l-0 border-r-0 focus:outline-none py-2 px-3"
                                           placeholder="ពាក្យសម្ងាត់ថ្មី"
                                           v-model="password"
                                           @keypress="isNumber($event)"

                                    />
                                    <input type="password"
                                           class="w-full border border-gray-200 border-t-0 border-l-0 border-r-0 focus:outline-none py-2 px-3 mt-5"
                                           placeholder="ពាក្យសម្ងាត់ថ្មីម្ដងទៀត"
                                           v-model="confirmPassword"
                                           @keypress="isNumber($event)"
                                    />

                                </div>
                                <div class="flex justify-end">
                                    <button class="bg-primary rounded px-5 py-3 text-white mt-5 font-khmer_os hover:bg-opacity-90 focus:outline-none"
                                            :disabled="changing"
                                            @click="changePassword"
                                    >
                                        កែប្រពាក្យសម្ងាត់
                                        <Loader :size="10" v-if="changing"/>
                                    </button>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
            <ErrMessage @closeErr="closeErr" :message="message" v-if="err"/>
        </div>
    </div>
</template>

<script>
    import UserProfileIcon from "./../../../components/UserProfileIcon"
    import CloseIcon from "./../../../components/CloseIcon"
    import helper from "./../../../helper/helper"
    import ErrMessage from "./../components/ErrMessage"
    import {mapState, mapActions} from "vuex"
    import Loader from "./../../../components/Loader"
    export default{
        name: "ChangePassword",
        components: {
            UserProfileIcon,
            CloseIcon,
            ErrMessage,
            Loader
        },

        data(){
            return {
                password: null,
                confirmPassword: null,
                err: false,
                message: null
            }
        },
        props: {
            size: {
                type: Number,
                default: function () {
                    return 96;
                }
            }
        },

        computed: {
            ...mapState('auth', ['changing', 'phone'])
        },

        methods: {
            ...mapActions('auth', ['changeForgotPassword']),
            isNumber(evt){
                return helper.isNumber(evt)
            },
            closeErr(){
                this.err = false
            },
            closeChangePassword($event){
                this.$emit("closeChangePassword", $event)
            },
            changePassword(){

                if (!this.password) {
                    this.message = "សូមបញ្ចូលពាក្យសម្ងាត់ថ្មី"
                    this.err = true
                    return false;
                }

                if (!this.confirmPassword) {
                    this.message = "សូមបញ្ចូលលេខសម្ងាត់ផ្ទៀងផ្ទាត់"
                    this.err = true
                    return;
                }

                if (this.confirmPassword !== this.password) {
                    this.message = "ពាក្យសម្ងាត់ និងផ្ទៀងផ្ទាត់ពាក្យសម្ងាត់មិនដូចគ្នា"
                    this.err = true
                    return;
                }
                this.changeForgotPassword({
                    phone: this.phone,
                    password: this.password,
                    confirm_password: this.confirmPassword,
                    device_id: helper.deviceId()
                }).then(response => {
                    if (response.status == 1) {
                        this.message = response.msg
                        this.err = true
                    } else {
                        this.$emit("changePasswordSuccess")
                    }
                })
            }
        }
    }
</script>