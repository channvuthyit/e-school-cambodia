<template>
    <div class="bg-white px-5 h-screen py-5">
        <div v-if="receipts && receipts.length<=0" class="flex justify-center h-screen">
            <img src="/icon/Empty/Empty.svg" class="w-64  mb-5 relative -top-14"/>
        </div>
        <div v-if="takingReceipt" class="flex justify-center items-center h-screen relative -top-5">
            <h1 class="text-sm font-semibold font-khmer_os relative -top-10">
                <loading></loading>
            </h1>
        </div>
        <div class="overflow-y-scroll h-screen" v-else>
            <div class="grid gap-4  mb-40" style="max-height:100vh"
                 :class="window.width <=1366?'grid-cols-3':window.width <=1440?'grid-cols-3':'grid-cols-4'"
            >
                <div v-for="(receipt,key) in receipts" :key="key">
                    <div class="flex rounded-xl border border-gray-300 p-3 items-center relative">
                        <div class="flex flex-1 z-40  cursor-pointer" @click="viewReceipt(receipt)">
                            <div class="mr-3">
                                <InvoiceIcon size="40" :fill="receipt.status !==1?'#000000':'#34D399'"></InvoiceIcon>
                            </div>
                            <div class="cursor-pointer">
                                <div>{{receipt.receipt}}</div>
                                <div class="text-13px text-gray-500">{{formatDate(receipt.date)}}</div>
                            </div>
                        </div>
                        <div class="absolute right-5 flex z-50">
                            <div class="font-semibold mr-2">${{receipt.amount}}</div>
                            <div class="cursor-pointer" style="margin-top:3px;" @click="removeReceipt(receipt._id)"
                                 v-if="receipt.status !==1">
                                <DeleteIcon></DeleteIcon>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
        <ReceiptInfo v-if="showInfo" :receiptDetail="receiptDetail" @closeInfo="closeInfo"
                     :message="message" :success="success"></ReceiptInfo>
        <ConfirmDelete v-if="showConfirm" @closeMessage="closeMessage"
                       @ConfirmDeleteCart="ConfirmDelete"></ConfirmDelete>
    </div>
</template>
<script>
    import {mapActions, mapState} from "vuex"
    import InvoiceIcon from "./../../components/InvoiceIcon"
    import DeleteIcon from "./../MyCourse/components/DeleteIcon"
    import ReceiptInfo from "./../MyCourse/components/ReceiptInfo"
    import ConfirmDelete from "./../MyCourse/components/ConfirmDelete"
    import moment from "moment"
    import Loading from "./../../components/Loading"
    export default{
        name: "Invoice",
        components: {
            InvoiceIcon,
            DeleteIcon,
            ReceiptInfo,
            ConfirmDelete,
            Loading
        },
        data(){
            return {
                window: {
                    width: 0,
                    height: 0
                },
                showConfirm: false,
                receiptId: "",
                showInfo: false,
                message: "ពត៍មានបង់ប្រាក់",
                active: 1,
                success: false,
            }
        },
        computed: {
            ...mapState('receipt', ['receipts', 'takingReceipt', 'receiptDetail', 'loadingReceipt']),
        },
        destroyed() {
            window.removeEventListener('resize', this.handleResize);
        },
        methods: {
            ...mapActions('receipt', ['deleteReceipt', 'getReceipt', 'getReceiptDetail']),

            handleResize() {
                this.window.width = window.innerWidth;
                this.window.height = window.innerHeight;
            },
            viewReceipt(receipt){
                if (receipt.status === 1) {
                    this.success = true
                } else {
                    this.success = false
                }
                this.message = "ពត៍មានបង់ប្រាក់"
                this.getReceiptDetail(receipt._id).then(() => {
                    this.showInfo = true
                })

            },
            closeInfo(){
                this.showInfo = false
            },
            removeReceipt(receiptId){
                this.showConfirm = true
                this.receiptId = receiptId
            },
            closeMessage(){
                this.showConfirm = false
            },
            ConfirmDelete(){
                this.deleteReceipt(this.receiptId).then(() => {
                    this.showConfirm = false
                })
            },
            formatDate(date){
                moment.locale('en')
                if (!date) {
                    return
                }
                return moment(date).format("DD-MM-YYY h:mm")
            }
        },
        created(){
            window.addEventListener('resize', this.handleResize);
            this.handleResize();
            this.getReceipt(0)
        }

    }
</script>