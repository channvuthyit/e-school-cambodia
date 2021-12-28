<template>
    <div>
        <div>
            <div class="fixed z-10 inset-0 overflow-y-auto" v-if="!err">
                <div class="flex items-end justify-center min-h-screen pt-4 px-4 pb-20 text-center sm:block sm:p-0">
                    <div class="fixed inset-0 transition-opacity" aria-hidden="true">
                        <div class="absolute inset-0 bg-gray-500 opacity-75"></div>
                    </div>
                    <span class="hidden sm:inline-block sm:align-middle sm:h-screen" aria-hidden="true">&#8203;</span>

                    <div class="inline-block align-bottom bg-white rounded text-center overflow-hidden shadow-xl transform transition-all sm:my-8 sm:align-middle w-72"
                         role="dialog" aria-modal="true" aria-labelledby="modal-headline">
                        <div class="bg-white px-4 pt-3">
                            <div class="flex justify-center items-center">
                                <div class="mt-3">
                                    <div class="mt-2">
                                        <p class="text-sm text-black text-sm font-khmer_os ">
                                            សូមបំពេញលេខទូរស័ព្ទរបស់អ្នកដើម្បីទទួលពាក្យសម្ងាត់ថ្មី
                                        </p>
                                    </div>
                                    <div class="mt-5 flex items-center relative">
                                        <input
                                                v-model="phone"
                                                @keypress="isNumber($event)"
                                                type="text"
                                                class="p-1 pl-2 border border-solid border-1 border-light-blue-500 w-full focus:outline-none  border rounded mb-4"
                                        />
                                        <img src="/ajax-loader.gif" class="absolute right-0 top-0 mt-2 mr-3"
                                             v-if="checking"/>
                                    </div>
                                </div>
                            </div>
                        </div>
                        <hr>
                        <div class="flex justify-between items-center font-khmer_os">
                            <button type="button" @click="cancel()"
                                    class="text-sm font-khmer_os mt-3 w-full inline-flex justify-center  px-4 py-2 text-base font-medium text-primary focus:outline-none sm:mt-0 sm:ml-3 sm:w-auto sm:text-sm">
                                បោះបង់
                            </button>
                            <div class="border border-1 border-gray-200 border-l-0 border-t-0 border-b-0 h-11"></div>
                            <button type="button" @click="agree($event)"
                                    class="w-full inline-flex justify-center  px-4 py-2  text-base font-medium text-primary focus:outline-none sm:ml-3 sm:w-auto sm:text-sm">
                                បាទ/ចាស់
                            </button>
                        </div>
                    </div>
                </div>
            </div>
        </div>
        <ErrMessage v-if="err" :message="message" @closeErr="closeErr"/>
        <ChangePassword v-if="changePass" @closeChangePassword="closeChangePassword"
                        @changePasswordSuccess="changePasswordSuccess"/>
    </div>
</template>
<script>
    import {mapActions, mapState} from "vuex"
    import ErrMessage from "./components/ErrMessage"
    import helper from "./../../helper/helper"
    import ChangePassword from "./components/ChangPassword"


    export default{
        name: "ForgotPassword",
        components: {
            ErrMessage,
            ChangePassword
        },

        data(){
            return {
                err: false,
                changePass: false,
                hideForgot: false,
                phone: null,
                message: "លេខទូរស័ព្ទមិនត្រឹមត្រូវ"
            }
        },

        computed: {
            ...mapState('auth', ['checking'])
        },

        methods: {
            ...mapActions('auth', ['checkPhoneExist', 'getPhone']),

            closeChangePassword(){
                this.changePass = false
            },
            changePasswordSuccess(){
                this.$emit("changePasswordSuccess")
            },
            isNumber(evt){
                return helper.isNumber(evt)
            },

            cancel(){
                this.$emit('cancel');
            },

            closeErr(){
                this.err = false
            },

            agree(){
                if (!this.phone) {
                    this.message = "សូមបញ្ចូលលេខទូរស័ព្ទ"
                    this.err = true
                    return;
                }

                this.checkPhoneExist(this.phone).then(response => {
                    if (response.data.exist == 0) {
                        this.message = "លេខទូរស័ព្ទមិនត្រឹមត្រូវ"
                        this.err = true
                    } else {
                        this.err = false
                        this.getPhone(this.phone)
                        this.changePass = true
                    }
                })
            },
        },
    }
</script>