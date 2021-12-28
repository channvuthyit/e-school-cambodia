<template>
    <div class="fixed inset-0 overflow-y-auto" style="z-index: 51">
        <div class="flex items-end justify-center min-h-screen text-center sm:block sm:p-0">
            <div class="fixed inset-0 transition-opacity" aria-hidden="true">
                <div class="absolute inset-0 bg-gray-500 opacity-75"></div>
            </div>
            <span class="hidden sm:inline-block sm:align-middle sm:h-screen" aria-hidden="true">&#8203;</span>
            <div class="inline-block align-bottom bg-white rounded-xl text-left overflow-hidden shadow-xl transform transition-all  sm:align-middle"
                 :class="`w-${size}`" role="dialog" aria-modal="true" aria-labelledby="modal-headline">
                <div class="header flex justify-between items-center p-4 border border-gray-200 border-t-0 border-l-0 border-r-0 text-sm font-khmer_os">
                    <div class="text-left flex-1 text-center font-semibold text-sm">
                        សារជូនដំណឹង
                    </div>
                    <div class="opacity-80 cursor-pointer" @click="closeNotification">
                        <CloseIcon :width="20" :height="20"></CloseIcon>
                    </div>
                </div>
                <div class="body mt-5 flex-cols justify-start items-center font-khmer_os text-13px"
                     style="height: 600px;overflow-y: scroll" @scroll="onScroll">
                    <div v-if="loadingNotification"
                         class="flex justify-center items-center h-screen">
                        <div class="relative" :class="window.width <= '1366'?' -top-20':' -top-56'">
                            <Loading/>
                        </div>
                    </div>
                    <div v-else>
                        <div v-if="notifications && notifications.length">
                            <ul>
                                <li v-for="(notification, key) in notifications" :key="key"
                                    class="flex justify-start items-center px-4 py-2 border border-gray-200 border-t-0 border-l-0 border-r-0 cursor-pointer"
                                    @click="readNotification(notification)">
                                    <img src="/icon/Notification/News.png" class="w-12 mr-3"/>
                                    <div class="flex-cols justify-start items-center flex-1">
                                        <div class="font-semibold mb-1">{{cutString(notification.title, 25)}}</div>
                                        <div>{{cutString(notification.content.text, 40)}}</div>
                                    </div>
                                    <div class="text-right relative top-5 opacity-50 text-xs">
                                        {{formatDate(notification.date)}}
                                    </div>
                                </li>
                            </ul>
                        </div>
                    </div>
                </div>

            </div>
        </div>
    </div>
</template>
<script>
    import {mapActions, mapState} from "vuex"
    import CloseIcon from "./../components/CloseIcon"
    import helper from "./../helper/helper"
    import moment from "moment"
    import Loading from "./Loading"
    export default{
        name: "Notification",
        data(){
            return {
                window: {
                    width: 0,
                    height: 0
                },
                page: 1
            }
        },
        props: {
            size: {
                type: Number,
                default: () => {
                    return 96
                }
            }
        },
        components: {
            CloseIcon,
            Loading
        },
        computed: {
            ...mapState('auth', ['loadingNotification', 'notifications'])
        },
        destroyed() {
            window.removeEventListener('resize', this.handleResize);
        },
        methods: {
            ...mapActions('auth', ['getNotification', 'readingNotification']),
            handleResize() {
                this.window.width = window.innerWidth;
                this.window.height = window.innerHeight;
            },

            onScroll ({target: {scrollTop, clientHeight, scrollHeight}}) {
                if (scrollTop + clientHeight >= scrollHeight) {
                    this.page++

                    this.getNotification(this.page)

                }
            },
            readNotification(notification){
                this.readingNotification(notification._id)
                this.$emit('readNotification', notification)
            },
            formatDate(date){
                moment.locale('en');
                return moment(date).format('DD-MM-YYYY h:mm');
            },
            closeNotification(){
                this.$emit("closeNotification")
            },
            cutString(text, limit){
                if (text) {
                    return helper.cutString(text, limit)
                }
                return ""

            }
        },

        created(){
            window.addEventListener('resize', this.handleResize);
            this.handleResize();
            this.getNotification(1)
        }
    }
</script>