<template>
    <div class="font-khmer_os font-14px h-90 overflow-y-scroll">
        <div class="flex justify-center items-center h-90" v-if="loadingPartner">
            <div>
                <loading></loading>
            </div>
        </div>
        <div v-else>
            <div class="grid gap-6 px-5 mt-5"
                 :class="window.width <= 1366?`grid-cols-3`:`grid-cols-4`"
                 v-if="(partners && partners.length)">
                <div v-for="(partner, index) in partners" :key="index"
                     :class="partner.type ===2?`col-span-4`:``"
                >
                    <div class="relative cursor-pointer bg-white shadow-md rounded-lg"
                         :class="partner.type ===2?`px-2`:``">
                        <div class="flex justify-start items-center" :class="partner.type ===2?`py-2 px-1`:`p-2`">
                            <div class="border border-opacity-10 h-10 w-10 shadow rounded-full flex justify-center items-center mr-2"
                                 :style="{backgroundColor:`#ffffff`,backgroundImage:`url(${partner.photo}`,backgroundSize:`cover`}">
                            </div>
                            <h3>{{partner.name}}</h3>
                        </div>
                        <div>
                            <div v-if="partner.type === 1">
                                <div v-for="(pk, key) in partner.packages" :key="key">
                                    <div class="flex justify-center p-0">
                                        <img :src="pk.thumbnail" class="img w-full rounded-b-lg"
                                             :style="{maxHeight:image.height?`${image.height}px`:''}"
                                             @click="openWebView(index,key,pk)"/>
                                    </div>
                                    <div class="flex px-3  justify-between bg-white rounded-b-lg items-center h-10 leading-10 font-semibold absolute left-0 bottom-0 w-full">
                                        <div class="flex text-14px">
                                            <div class="text-custom mr-2">តម្លៃ:</div>
                                            <del v-if="pk.price.discount" class="mr-2">
                                                ${{pk.price.discount}}
                                            </del>
                                            <div class="text-red-700">
                                                ${{pk.price.year}}
                                            </div>
                                        </div>

                                        <div v-if="!pk.is_in_cart && pk.price.year"
                                             @click="addToCart(index,key,pk._id)">
                                            <CartIcon :size="20" fill="none"></CartIcon>
                                        </div>
                                    </div>
                                </div>
                            </div>
                            <div v-if="partner.type === 2">
                                <vue-horizontal responsive>
                                    <!--<div class="grid grid-flow-col">-->
                                    <section v-for="(pk, key) in partner.packages" :key="key"
                                             style="white-space: nowrap; width: auto" class="relative">
                                        <div>
                                            <img :src="pk.thumbnail" class="rounded-b-lg"
                                                 :style="{maxHeight:image.height?`${image.height}px`:'0px'}"
                                                 @click="openWebView(index,key,pk)"/>
                                            <div class="flex px-2  justify-between bg-white items-center h-10 leading-10 font-semibold absolute left-0 bottom-0 w-full">
                                                <div class="flex text-14px">
                                                    <div class="text-custom mr-2">តម្លៃ:</div>
                                                    <del v-if="pk.price.discount" class="mr-2">
                                                        ${{pk.price.discount}}
                                                    </del>
                                                    <div class="text-red-700">
                                                        ${{pk.price.year}}
                                                    </div>
                                                </div>

                                                <div v-if="!pk.is_in_cart && pk.price.year"
                                                     @click="addToCart(index,key,pk._id)">
                                                    <CartIcon :size="20" fill="none"></CartIcon>
                                                </div>
                                            </div>

                                        </div>
                                    </section>
                                    <!--</div>-->
                                </vue-horizontal>

                            </div>

                        </div>
                    </div>
                </div>
            </div>
        </div>
        <Message v-if="errMessage" :message="errMessage" @closeMessage="closeMessage" @showCart="showCart"></Message>
        <Cart v-if="showCartForm" @closeCart="closeCart"></Cart>
    </div>
</template>
<script>
    import {mapState, mapActions} from "vuex"
    import Loading from "./../../components/Loading"
    import CartIcon from "./../../components/CartIcon"
    import VueHorizontal from "vue-horizontal";
    import Message from "./../MyCourse/components/Message.vue"
    import Cart from "./../MyCourse/components/Cart"
    const {ipcRenderer} = require('electron')
    import err from "./../../helper/err"
    export default{
        components: {
            Loading,
            CartIcon,
            VueHorizontal,
            Message,
            Cart
        },
        data(){
            return {
                image: {
                    height: 0
                },
                window: {
                    width: 0,
                    height: 0
                },
                errMessage: "",
                course_id: "",
                showCartForm: false,
                index: "",
                key: "",
            }
        },
        computed: {
            ...mapState('partner', ['partners', 'loadingPartner']),
            removedCart(){
                return this.$store.state.cart.removedCart
            },
        },
        methods: {
            ...mapActions('partner', ['getPartner', 'openPartner']),
            ...mapActions('cart', ['addCart', 'getCart']),
            ...mapActions('course', ['afterAddToCart']),

            addToCart(index, key, course_id){
                this.addCart(course_id).then(() => {
                    this.$store.commit("partner/addCartToPartner", {index, key})

                    this.afterAddToCart(course_id)
                    this.getCart()
                })
            },
            openWebView(index, key, partner){
                this.course_id = partner._id
                this.index = index
                this.key = key

                this.openPartner(partner._id).then(response => {
                    if (response.status !== undefined && response.status === 1) {
                        if (response.msg === 'វគ្គសិក្សាមិនត្រឹមត្រូវ') {
                            return
                        }
                        this.errMessage = response.msg
                    } else {
                        if (response.data.msg !== undefined) {
                            err.congratulation(response.data.msg, response.data.url)
                        } else {
                            ipcRenderer.send("openLink", response.data.url)
                        }

                    }
                })
            },
            closeMessage(){
                this.errMessage = ""
            },
            showCart(){
                this.errMessage = ""
                this.addCart(this.course_id).then(() => {

                    this.showCartForm = true
                    this.$store.commit("partner/addCartToPartner", {index: this.index, key: this.key})
                    this.afterAddToCart(this.course_id)
                    this.getCart()
                })
            },
            closeCart(){
                this.showCartForm = false
            },
            handleResize() {
                this.window.width = window.innerWidth;
                this.window.height = window.innerHeight;
            },
            receiveParther(){
                this.getPartner().then(() => {
                    let interval = setInterval(() => {
                        let img = document.getElementsByClassName('img')[0].height
                        if (img) {
                            clearInterval(interval)
                            this.image.height = img
                        }
                    }, 100)
                })
            }
        },
        destroyed() {
            window.removeEventListener('resize', this.handleResize);
        },
        created(){
            this.receiveParther()
            window.addEventListener('resize', this.handleResize);

            this.handleResize();
        },
        watch: {
            removedCart: function (id) {
                if (id) {
                    this.receiveParther()
                }
            }
        }
    }
</script>