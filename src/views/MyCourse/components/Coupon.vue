<template>
    <div class="fixed inset-0 overflow-y-auto" style="z-index: 52">
        <div class="flex items-end justify-center min-h-screen text-center sm:block sm:p-0" v-if="!showErr">
          
            <div class="fixed inset-0 transition-opacity" aria-hidden="true">
                <div class="absolute inset-0 bg-gray-500 opacity-75"></div>
            </div>

            <span class="hidden sm:inline-block sm:align-middle sm:h-screen" aria-hidden="true">&#8203;</span>
          
            <div class="w-64 inline-block align-bottom bg-white rounded-xl text-left overflow-hidden shadow-xl transform transition-all  sm:align-middle"
                 role="dialog" aria-modal="true" aria-labelledby="modal-headline">
                <div class="bg-white">
                    <div class="flext justify-center items-center">
                        <div class="text-center">
                            <div class="p-5">
                                <p class="text-sm text-gray-500 text-sm font-khmer_os mb-5">
                                    សូមបញ្ចូលលេខគុប៉ុង
                                </p>
                                <div class="relative">
                                    <input type="text"
                                           v-model="code"
                                           class="rounded-md border border-gray-300 p-1 focus:outline-none w-full"/>
                                    <img src="ajax-loader.gif" class="absolute right-0 top-0 mt-2 mr-5"
                                         v-if="checkingCoupon">
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
                <hr>
                <div class="sm:flex sm:flex-row text-sm font-khmer_os h-10 justify-center items-center">
                    <button type="button" @click="closeCoupon"
                            class="w-full inline-flex justify-center  px-4   text-base font-medium text-primary focus:outline-none sm:w-auto sm:text-sm">
                        បោះបង់
                    </button>
                    <div class="h-10 border border-gray-300 border-l-0 border-t-0 border-b-0" style="width:0px;"></div>
                    <button type="button" @click="checkCouponExist" :disabled="checkingCoupon"
                            class="w-full inline-flex justify-center  px-4   text-base font-medium text-primary focus:outline-none sm:ml-3 sm:w-auto sm:text-sm">
                        បាទ/ចាស់
                    </button>
                </div>
            </div>
        </div>
        <CouponErr v-if="showErr" :message="messageError" :size="size" @closeCouponErr="closeCouponErr"></CouponErr>
    </div>
</template>
<script>
    import {mapActions, mapState} from "vuex"
    import CouponErr from "./CouponErr.vue"
    export default{
        name: "Coupon",
        components: {
            CouponErr
        },
        data(){
            return {
                code: null,
                messageError: null,
                size: 64,
                showErr: false
            }
        },

        computed: {
            ...mapState('cart', ['checkingCoupon'])
        },

        methods: {
            ...mapActions('cart', ['checkCoupon']),
            closeCoupon($event){
                this.$emit("closeCoupon", $event)
            },

            checkCouponExist(){
                if (!this.code) {
                    this.showErr = true
                    this.messageError = "សូមធ្វើការបញ្ចូល គូប៉ុង"
                    return
                }
                this.checkCoupon(this.code).then(response => {
                    if (response.status == 1) {
                        this.showErr = true
                        this.messageError = response.msg
                    } else {
                        this.$emit("couponExist", this.code)
                    }
                })
            },

            closeCouponErr(){
                this.showErr = false
            }
        },

        created(){
        }
    }
</script>