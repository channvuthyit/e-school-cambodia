<template>
    <div class="bg-white">
        <div class="flex justify-between px-5 pt-3">
            <div class="menu">
                <ul class="flex font-khmer_os items-center justify-between text-base">
                    <li class="text-center mr-5 hover:text-custom pointer cursor-pointer"
                        @mouseover="icons.home.original = icons.home.hover"
                        @mouseout="icons.home.original = icons.home.out"
                        :class="$route.name == 'home'?'text-custom':''" @click="goTo('home')">
                        <div class="flex justify-center items-center w-full">
                            <HomeIcon :fill="$route.name == 'home'?icons.home.hover:icons.home.original"></HomeIcon>
                        </div>
                        <p class="mt-2">ទំព័រដើម</p></li>
                    <li class="flex-cols justify-center items-center mr-5 hover:text-custom pointer cursor-pointer"
                        @click="goTo('video')"
                        @mouseover="icons.video.original = icons.video.hover"
                        @mouseout="icons.video.original = icons.video.out"
                        :class="$route.name == 'video'?'text-custom':''"
                    >
                        <div class="flex justify-center items-center w-full">
                            <VideoIcon
                                    :fill="$route.name == 'video'?icons.video.hover:icons.video.original"></VideoIcon>
                        </div>
                        <p class="mt-2"> វីដេអូ</p></li>
                    <li class="text-center mr-5 hover:text-custom pointer cursor-pointer" @click="goTo('book')"
                        @mouseover="icons.book.original = icons.book.hover"
                        @mouseout="icons.book.original = icons.book.out"
                        :class="$route.name == 'book'?'text-custom':''">
                        <div class="flex justify-center items-center w-full">
                            <BookIcon :fill="$route.name == 'book'?icons.book.hover:icons.book.original"></BookIcon>
                        </div>
                        <p class="mt-2">សៀវភៅ</p></li>
                    <li class="text-center mr-5 hover:text-custom pointer cursor-pointer" @click="goTo('partner')"
                        @mouseover="icons.partner.original = icons.partner.hover"
                        @mouseout="icons.partner.original = icons.partner.out"
                        :class="$route.name == 'partner'?'text-custom':''">
                        <div class="flex justify-center items-center w-full">
                            <PartnershipIcon :fill="$route.name == 'partner'?icons.partner.hover:icons.partner.original"></PartnershipIcon>
                        </div>
                        <p class="mt-2">ដៃគូ</p></li>
                    <li class="text-center mr-5 hover:text-custom pointer cursor-pointer" @click="goTo('discuss')"
                        @mouseover="icons.discuss.original = icons.discuss.hover"
                        @mouseout="icons.discuss.original = icons.discuss.out"
                        :class="$route.name == 'discuss'?'text-custom':''">
                        <div class="flex justify-center items-center w-full">
                            <DiscussionIcon
                                    :fill="$route.name == 'discuss'?icons.discuss.hover:icons.discuss.original"></DiscussionIcon>
                        </div>
                        <p class="mt-2">ពិភាក្សា</p></li>
                    <li class="text-center hover:text-custom pointer cursor-pointer" @click="goTo('test')"
                        @mouseover="icons.test.original = icons.test.hover"
                        @mouseout="icons.test.original = icons.test.out"
                        :class="$route.name == 'test'?'text-custom':''">
                        <div class="flex justify-center items-center w-full">
                            <TestIcon :fill="$route.name == 'test'?icons.test.hover:icons.test.original"></TestIcon>
                        </div>
                        <p class="mt-2"> តេស្ត</p></li>

                </ul>
            </div>
            <div>
                <ul class="flex justify-end items-center mt-3">
                    <li class="mr-5 cursor-pointer relative" @click="showCartForm">
                        <span v-if="cart && cart.list && cart.list.length"
                              class="rounded-full w-5 h-5 bg-red-500 absolute -top-3 -right-3 text-white flex justify-center items-center text-13px">
                            {{cart.list.length}}
                        </span>
                        <CartIcon/>
                    </li>
                    <li class="cursor-pointer" @click="showNotificationForm">
                        <NotificationIcon/>
                    </li>
                </ul>
            </div>
        </div>
        <div class="border border-b-0 border-l-0 border-r-0 mt-2 border-gray-200 pl-3">
            &nbsp;
        </div>
        <Cart v-if="showCart" @closeCart="closeCart"></Cart>
        <Notification v-if="showNotification" @closeNotification="closeNotification"
                      @readNotification="readNotification($event)"></Notification>
        <NotificationDetail v-if="showNotificationDetail" :notification="notification"
                            @closeNotificationDetail="closeNotificationDetail"></NotificationDetail>

    </div>
</template>

<script>
    import HomeIcon from "./../../components/HomeIcon"
    import BookIcon from "./../../components/BookIcon"
    import VideoIcon from "./../../components/VideoIcon"
    import TestIcon from "./../../components/TestIcon"
    import DiscussionIcon from "./../../components/DiscussionIcon"
    import PartnershipIcon from "./../../components/PartnershipIcon"
    import CartIcon from "./../../components/CartIcon"
    import NotificationIcon from "./../../components/NotificationIcon"
    import {mapState, mapActions} from "vuex"
    import Cart from "./../MyCourse/components/Cart.vue"
    import Notification from "./../../components/Notification"
    import NotificationDetail from "./../../components/NotificationDetail"


    export default {
        components: {
            HomeIcon,
            BookIcon,
            VideoIcon,
            TestIcon,
            NotificationIcon,
            CartIcon,
            Cart,
            Notification,
            NotificationDetail,
            DiscussionIcon,
            PartnershipIcon
        },
        data(){
            return {
                showCart: false,
                showNotification: false,
                showNotificationDetail: false,
                baseIconUrl: '/icon/',
                notification: {},
                icons: {
                    home: {
                        original: '#000000',
                        out: '#000000',
                        hover: '#00a0e4'
                    },
                    video: {
                        original: '#000000',
                        out: '#000000',
                        hover: '#00a0e4'
                    },
                    book: {
                        original: '#000000',
                        out: '#000000',
                        hover: '#00a0e4'
                    },
                    test: {
                        original: '#000000',
                        out: '#000000',
                        hover: '#00a0e4'
                    },
                    discuss: {
                        original: '#000000',
                        out: '#000000',
                        hover: '#00a0e4'
                    },
                    partner: {
                        original: '#000000',
                        out: '#000000',
                        hover: '#00a0e4'
                    }
                }
            }
        },
        computed: {
            ...mapState('cart', ['cart']),
            cartAdded: {
                get(){
                    return this.$store.state.cart.cartAdded
                },
                set(cartAdded){
                    return cartAdded
                }
            }
        },
        methods: {
            ...mapActions('cart', ['getCart']),
            showCartForm(){
                this.showCart = true
            },
            closeNotification(){
                this.showNotification = false
            },
            showNotificationForm(){
                this.showNotification = true
            },
            readNotification($event){
                this.notification = $event
                this.showNotificationDetail = true
//                this.showNotification = false
            },
            closeNotificationDetail(){
                this.showNotificationDetail = false
//                this.showNotification = true
            },
            closeCart(){
                this.showCart = false
            },
            goTo(route){
                if (route !== this.$route.name) {
                    this.$router.push({name: `${route}`})
                }

            }
        },

        created(){
            this.getCart()
        },
        watch: {
            cartAdded: function () {
                this.getCart()
            }
        }
    };
</script>

<style>
</style>
