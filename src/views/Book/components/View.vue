<template>
    <div class="fixed z-50 inset-0 overflow-y-auto">
        <div class="flex items-end justify-center min-h-screen text-center sm:block sm:p-0">
            <div class="fixed inset-0 transition-opacity" aria-hidden="true">
                <div class="absolute inset-0 bg-gray-500 opacity-75"></div>
            </div>
            <span class="hidden sm:inline-block sm:align-middle sm:h-screen" aria-hidden="true">&#8203;</span>
            <div class="inline-block align-bottom bg-white rounded-xl text-left overflow-hidden shadow-xl transform transition-all  sm:align-middle"
                 :class="`w-${size}`" role="dialog" aria-modal="true" aria-labelledby="modal-headline">
                <div class="bg-white font-khmer_os relative">
                    <div class="flex-cols items-center">
                        <div class="flex justify-between items-center p-5">

                            <div class="flex-1 text-center">{{view.title}}</div>
                            <div class="cursor-pointer opacity-60" @click="closeView">
                                <CloseIcon :width="20" :height="20"></CloseIcon>
                            </div>
                        </div>
                        <div class="relative pb-10" style="max-height: 600px;overflow-y: scroll">
                            <div class="bg-black py-5 relative">
                                <img :src="view.thumbnail" class="m-auto" style="max-width:50%"/>
                                <template v-if="!alreadyInCart(view._id ) && !view.is_in_cart">
                                    <div class="absolute bottom-5 right-5 bg-white rounded py-1 px-2 cursor-pointer opacity-80 hover:opacity-100"
                                         v-if="view.is_buy !==0" @click="addToCart(view._id)">
                                        <CartIcon :width="20" :height="20"></CartIcon>
                                    </div>
                                </template>
                            </div>
                            <div class="text-center text-custom p-2 font-medium cursor-pointer flex justify-center items-center"
                                 @click="read(view._id,view.title)">
                                អាន <img src="/ajax-loader.gif" v-if="loading" class="ml-2"/>
                            </div>
                            <hr>
                            <div class="p-5">
                                <p>{{view.title}}</p>
                                <p>ម្ចាស់សៀវភៅ៖ {{view.teacher.name}}</p>
                                <p class="mt-5 mr-5 text-13px" v-html="view.des" style="white-space: pre-wrap"></p>
                            </div>
                        </div>

                    </div>

                    <button class="fixed bottom-0 bg-blue w-full p-3 bg-custom text-center text-white cursor-pointer focus:outline-none"
                            :disabled="loadingCart"
                            @click="addToCart(view._id)" v-if="view.price.year">
                        <span v-text="view.is_buy?'បន្តសុពលភាព':'ទិញ'"></span> <span v-if="loadingCart"><Loader
                            :size="10"></Loader></span>
                    </button>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
    import CloseIcon  from "./../../../components/CloseIcon.vue"
    import CartIcon from "./../../../components/CartIcon.vue"
    import {mapActions, mapState} from "vuex"
    import Loader from "./../../../components/Loader.vue"


    export default{
        name: "ViewBook",
        components: {
            CloseIcon,
            CartIcon,
            Loader
        },
        props: {
            cartId: {
                type: String,
                default: () => {
                    return ""
                }
            },
            loading: {
                type: Boolean,
                default: function () {
                    return false
                }
            },
            view: {
                type: Object,
            },
            size: {
                type: Number,
                default: () => {
                    return 96;
                }
            },
            hide: {
                type: Boolean,
                default: function () {
                    return false
                }
            },
        },

        computed: {
            ...mapState('cart', ['loadingCart', 'cart'])
        },
        methods: {
            ...mapActions('cart', ['addCart', 'getCart']),

            addToCart(course_id){
                this.addCart(course_id).then(() => {
                    this.$emit('buyingRead', course_id)
                    this.getCart()
                })
            },
            closeView($event){
                this.$emit("closeView", $event);
            },
            read(id, title){
                this.$emit('read', {id, title})
            },

            showCart($event){
                this.$emit("showCart", $event);
            },
            alreadyInCart(course_id){
                for (let i = 0; i < this.cart.list.length; i++) {
                    if (this.cart.list[i]._id === course_id) {
                        return true
                    }
                }
                return false
            }
        },
        created(){
            this.getCart()
        }
    }
</script>