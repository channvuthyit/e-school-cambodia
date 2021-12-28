<template>
    <div class="fixed inset-0 overflow-y-auto" style="z-index: 52">
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
                    <div class="opacity-80 cursor-pointer" @click="closeNotificationDetail">
                        <CloseIcon :width="20" :height="20"></CloseIcon>
                    </div>
                </div>
                <div class="body mt-5 flex-cols justify-start items-center font-khmer_os text-13px" style="height: 600px;overflow-y: scroll">
                    <div class="px-3">
                        <div class="font-semibold mb-3">{{notification.title}}</div>
                        <div>{{notification.content.text}}</div>
                        <div class="opacity-50 text-right text-xs">{{formatDate(notification.date)}}</div>
                    </div>
                </div>

            </div>
        </div>
    </div>
</template>
<script>
    import CloseIcon from "./../components/CloseIcon"
    import moment from "moment"
    export default{
        name: "Notification",
        data(){
            return {
                page: 1
            }
        },
        props: {
            size: {
                type: Number,
                default: () => {
                    return 96
                }
            },
            notification: {
                type: Object
            }
        },
        components: {
            CloseIcon
        },
        computed: {},
        methods: {
            formatDate(date){
                return moment(date).format('DD-MM-YYYY h:mm');
            },
            closeNotificationDetail(){
                this.$emit("closeNotificationDetail")
            }
        },

    }
</script>