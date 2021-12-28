<template>
    <div>
        <div class="fixed inset-0" v-if="!showPaymentMethodForm" style="z-index:51">
            <div class="flex items-end justify-center min-h-screen pt-4 px-4 pb-20 text-center sm:block sm:p-0"
                 v-if="!showCouponForm">
                <div class="fixed inset-0 transition-opacity" aria-hidden="true">
                    <div class="absolute inset-0 bg-black opacity-75"></div>
                </div>

                <!-- This element is to trick the browser into centering the modal contents. -->
                <span class="hidden sm:inline-block sm:align-middle sm:h-screen" aria-hidden="true">&#8203;</span>
                <div class="inline-block align-bottom bg-white rounded-xl text-left overflow-hidden shadow-xl transform transition-all sm:my-8 sm:align-middle sm:max-w-lg sm:w-full"
                     role="dialog" aria-modal="true" aria-labelledby="modal-headline">
                    <div class="bg-white px-5 py-4">
                        <div class="flex items-center justify-between">
                            <div class="flex justify-center">
                                <CartIcon></CartIcon>
                                <h2 class="font-khmer_os text-sm mt-2 pl-1 font-semibold">កន្ត្រក</h2>
                            </div>
                            <div class="cursor-pointer" @click="closeCart">
                                <CloseIcon></CloseIcon>
                            </div>
                        </div>
                    </div>
                    <hr>
                    <div v-if="loadingCart" class="flex justify-center items-center">
                        <div class="mt-3">
                            <Loading/>
                        </div>
                    </div>
                    <div v-else>
                        <div v-if="cart.list">
                            <template v-if="cart.list.length <= 0">
                                <h2 class="flex justify-center items-center text-md font-khmer_os my-5">
                                    <span>អ្នកមិនមានមេរៀននៅក្នុងកន្រ្តកទេ</span>
                                </h2>
                            </template>
                            <template v-else>
                                <div style="max-height: 70vh;overflow-y: scroll">
                                    <div class="flex px-5 py-3 border border-t-0 border-l-0 border-r-0 border-gray-100"
                                         v-for="(ct, key) in cart.list" :key="key"
                                         style="overflow:hidden;">
                                        <div class="w-1/3 mr-5"
                                        >
                                            <!--:style="{backgroundImage:`url(${ct.thumbnail})`,backgroundSize:'100% auto'}"-->
                                            <img :src="ct.thumbnail" class="w-full images"/>
                                        </div>
                                        <div class="flex justify-between w-2/3">
                                            <div class="flex-cols">
                                                <h2 class="text-sm font-khmer_os" :title="ct.title">
                                                    {{cutString(ct.title, 25)}}</h2>
                                                <h3 class="text-sm font-khmer_os text-gray-500 mt-1">
                                                    {{ct.teacher.name}}</h3>
                                            </div>
                                            <div class="flex flex-col ml-5 justify-between h-full text-right">
                                                <div class="cursor-pointer flex justify-end flex-1"
                                                     @click="deleteCartList(ct._id)">
                                                    <DeleteIcon></DeleteIcon>
                                                </div>
                                                <div class="flex justify-between items-between">
                                                    <span class="relative right-5 text-gray-500 text-14px font-khmer_os"
                                                          :class="ct.price.month?'':'text-red-500'">
                                                        {{durationCalculate((ct.price.duration ? ct.price.duration : 12), ct.price.month, ct.price.year)}}
                                                    </span>
                                                    <div class="flex-cols relative"
                                                         @click="ct.price.month?duration((ct.price.duration ? ct.price.duration : 12),ct._id):false">
                                                        <div class="transform rotate-180 cursor-pointer">
                                                            <Chevron></Chevron>
                                                        </div>
                                                        <div class="cursor-pointer">
                                                            <Chevron></Chevron>
                                                        </div>
                                                    </div>
                                                </div>
                                            </div>
                                        </div>
                                    </div>
                                </div>
                                <div class="bg-gray-50 px-4 py-5 sm:px-6 sm:flex sm:flex-row-reverse font-khmer_os">
                                    <button type="button"
                                            @click="showPaymentMethod"
                                            class="w-full inline-flex justify-center rounded-full px-3 py-2 bg-custom text-base font-medium text-white hover:bg-opacity-80 focus:outline-none   sm:ml-3 sm:w-auto sm:text-sm">
                                        ${{totalCart}} បង់ប្រាក់
                                    </button>
                                    <button type="button"
                                            @click="showCoupon"
                                            v-if="!cart.coupon"
                                            class="mt-3 w-full inline-flex justify-center rounded-full  px-3 py-2 bg-custom text-base font-medium text-white hover:bg-opacity-80 focus:outline-none  sm:mt-0 sm:ml-3 sm:w-auto sm:text-sm">
                                        បញ្ចូលលេខគូប៉ុង
                                    </button>
                                    <button type="button"
                                            @click="removeCoupon"
                                            class="mt-3 w-full inline-flex justify-center rounded-full  px-3 py-2 bg-red-600 text-base font-medium text-white hover:bg-opacity-80 focus:outline-none  sm:mt-0 sm:ml-3 sm:w-auto sm:text-sm"
                                            v-else>
                                        លុបលេខគូប៉ុង({{cart.coupon.code}})
                                    </button>
                                </div>
                            </template>
                        </div>
                    </div>
                </div>
            </div>
        </div>
        <Coupon
                v-if="showCouponForm"
                @closeCoupon="closeCoupon"
                @couponExist="couponExist">
        </Coupon>
        <ChooseDuration
                v-if="showDurationForm"
                @closeDuration="closeDuration"
                @choseDuration="choseDuration"
                :duration="durationSelected">
        </ChooseDuration>
        <ConfirmDelete v-if="showConfirmForm"
                       @ConfirmDeleteCart="ConfirmDeleteCart"
                       @closeMessage="closeMessage">
        </ConfirmDelete>
        <RemoveCoupon v-if="showRemoveCouponForm"
                      @closeRemoveCoupon="closeRemoveCoupon"
                      @ConfirmDeleteCoupon="ConfirmDeleteCoupon">
        </RemoveCoupon>
        <PaymentMethod v-if="showPaymentMethodForm" @closePaymentMethod="closePaymentMethod"></PaymentMethod>
    </div>
</template>

<script>
    import CartIcon from "./../../../components/CartIcon"
    import DeleteIcon from "./DeleteIcon"
    import Chevron from "./Chevron"
    import CloseIcon from "./CloseIcon"
    import Coupon from "./Coupon.vue"
    import {mapActions, mapState}  from  "vuex"
    import helper from "./../../../helper/helper"
    import Loading from "./../../../components/Loading"
    import ChooseDuration from "./../components/ChooseDuration.vue"
    import ConfirmDelete from "./../components/ConfirmDelete.vue"
    import RemoveCoupon from "./../components/RemoveCoupon.vue"
    import PaymentMethod from "./../components/PaymentMethod.vue"

    export default{
        name: "Cart",
        data(){
            return {
                showCouponForm: false,
                showDurationForm: false,
                showConfirmForm: false,
                showRemoveCouponForm: false,
                showPaymentMethodForm: false,
                totalCart: null,
                course_id: null,
                coupon: null,
                durationSelected: 12,
                changeDurationId: null
            }
        },

        components: {
            CartIcon,
            DeleteIcon,
            Chevron,
            CloseIcon,
            Coupon,
            Loading,
            ChooseDuration,
            ConfirmDelete,
            RemoveCoupon,
            PaymentMethod

        },

        computed: {
            ...mapState('cart', ['cart', 'loadingCart'])
        },

        methods: {
            ...mapActions('cart', ['getCart', 'removeCart', 'deleteCoupon', 'updateDuration']),

            closePaymentMethod($event){
                this.showPaymentMethodForm = false
                this.closeCart($event)
            },
            showPaymentMethod(){
                this.showPaymentMethodForm = true
            },
            cutString(text, limit){
                return helper.cutString(text, limit)
            },

            closeCart($event){
                this.$emit("closeCart", $event)
            },

            showCoupon(){
                this.showCouponForm = true
            },

            closeCoupon(){
                this.showCouponForm = false
            },

            deleteCartList(course_id){
                this.showConfirmForm = true
                this.course_id = course_id
            },

            ConfirmDeleteCart(){
                this.removeCart(this.course_id).then(() => {
                    this.totalCartPrice()
                    this.showConfirmForm = false
                })
            },

            closeMessage(){
                this.showConfirmForm = false
            },

            duration(duration, _id){
                this.changeDurationId = _id
                this.durationSelected = duration
                this.showDurationForm = true
            },

            closeDuration(){
                this.showDurationForm = false
            },

            choseDuration($event){
                this.updateDuration({
                    duration: $event,
                    _id: this.changeDurationId
                }).then(() => {
                    this.totalCartPrice()
                })
                this.showDurationForm = false
            },

            durationCalculate(duration, pricePerMonth, pricePerYear){
                return helper.durationCalculate(duration, pricePerMonth, pricePerYear)
            },

            removeCoupon(){
                this.showRemoveCouponForm = true
            },
            closeRemoveCoupon(){
                this.showRemoveCouponForm = false
            },
            ConfirmDeleteCoupon(){
                this.deleteCoupon().then(() => {
                    this.getCart().then(() => {
                        this.totalCartPrice()
                        this.showRemoveCouponForm = false
                    })

                })
            },

            couponExist($event){
                this.coupon = $event
                this.getCart().then(() => {
                    this.totalCartPrice()
                    this.showCouponForm = false
                })
            },

            totalCartPrice(){
                var total = 0;

                for (let index = 0; index < this.cart.list.length; index++) {
                    if (this.cart.list[index].price.duration) {
                        if (12 === this.cart.list[index].price.duration) {
                            total += this.cart.list[index].price.year
                        } else {
                            total += (this.cart.list[index].price.month * this.cart.list[index].price.duration)
                        }
                    } else {
                        total += this.cart.list[index].price.year
                    }
                }
                this.totalCart = total
            }
        },

        created(){
            this.getCart().then(() => {
                this.totalCartPrice()
            })
        }
    }
</script>