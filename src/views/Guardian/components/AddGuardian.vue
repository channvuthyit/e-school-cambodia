<template>
    <div>
        <div class="fixed z-10 inset-0 overflow-y-auto" v-if="!err">
            <div class="flex items-end justify-center min-h-screen text-center sm:block sm:p-0">
                <div class="fixed inset-0 transition-opacity" aria-hidden="true" @click="closeAdd">
                    <div class="absolute inset-0 bg-black opacity-75"></div>
                </div>
                <span class="hidden sm:inline-block sm:align-middle sm:h-screen" aria-hidden="true">&#8203;</span>
                <div class="inline-block align-bottom bg-opacity-0 rounded-xl text-left overflow-hidden  transform transition-all  sm:align-middle"
                     :class="`w-96`" role="dialog" aria-modal="true" aria-labelledby="modal-headline">
                    <div class="bg-opacity-0">
                        <div class="flex-cols justify-center items-center overflow-y-scroll bg-white font-khmer_os text-14px">
                            <div class="p-5">
                                <div class="header flex justify-between items-center mb-3">
                                    <div class="font-semibold text-base">សូមបំពេញពត៍មានមួយចំនួនខាងក្រោម</div>
                                    <div @click="closeAdd" class="cursor-pointer relative -top-1 -right-2">
                                        <CloseIcon :width="20" :height="20"></CloseIcon>
                                    </div>
                                </div>

                            </div>
                            <div class="body px-5">
                                <div class="flex justify-start items-center">
                                    <div class="w-20 mr-3">លេខទូរស័ព្ទ</div>
                                    <input type="text"
                                           class="border border-gray-300 border-l-0 border-r-0 border-t-0 py-2 focus:outline-none flex-1"
                                           placeholder="លេខទូរស័ព្ទ" v-model="phone" @keypress="isNumber($event)"/>
                                </div>
                                <div class="flex justify-start items-center my-5">
                                    <div class="w-20 mr-3">ត្រូវជា</div>
                                    <input type="text"
                                           class="border border-gray-300 border-l-0 border-r-0 border-t-0 py-2 focus:outline-none flex-1"
                                           placeholder="ត្រូវជា" v-model="asTo"/>
                                </div>
                                <button class="bg-custom text-center rounded py-2 w-full text-white text-lg focus:outline-none mb-5"
                                        :disabled="loadingAdd"
                                        @click="addGuardian">
                                    <span>Ok</span>
                                    <Loader :size="14" v-if="loadingAdd"></Loader>
                                </button>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
        <Message v-if="err" :message="message" @closeMessage="closeMessage"></Message>
    </div>
</template>

<script>

    import {mapActions, mapState} from "vuex"

    import CloseIcon from "./../../../components/CloseIcon"
    import Message from "./../../Profile/components/Message"
    import helper from "./../../../helper/helper"
    import Loader from "./../../../components/Loader"

    export default{
        name: "AddGuardian",
        components: {
            CloseIcon,
            Message,
            Loader
        },
        data(){
            return {
                phone: "",
                asTo: "",
                err: false,
                message: ""
            }
        },
        computed: {
            ...mapState('guardian', ['loadingAdd']),
        },
        methods: {
            ...mapActions('guardian', ['addingGuardian']),
            closeAdd(){
                this.$emit("closeAdd")
            },
            isNumber(evt){
                return helper.isNumber(evt)
            },
            closeMessage(){
                this.err = false
            },
            addGuardian(){
                if (this.phone.trim() === "") {
                    this.err = true
                    this.message = "សូមបញ្ចូលលេខទូរស័ព្ទអាណាព្យាបាល"
                    return
                }
                if (this.asTo.trim() === "") {
                    this.err = true
                    this.message = "សូមបញ្ជាក់ពីទំនាក់ទំនងជាមួយអាណាព្យាបាល"
                    return
                }
                this.addingGuardian({
                    phone: this.phone,
                    type: this.asTo
                }).then(response => {
                    if (response.data.status === 1) {
                        this.err = true
                        this.message = response.data.msg
                    }else{
                        this.$emit("GuardianAdded")
                    }
                })
            }

        },
        created(){

        },

    }
</script>