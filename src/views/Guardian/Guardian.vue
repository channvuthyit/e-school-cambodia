<template>
    <div class="bg-white h-screen pb-20 px-5 relative py-5">
        <div v-if="loadingGuardian" class="flex justify-center items-center h-screen relative -top-5">
            <h1 class="text-sm font-semibold font-khmer_os relative -top-10">
                <loading></loading>
            </h1>
        </div>
        <div v-else>
            <button class="z-50 cursor-pointer bg-custom w-10 h-10 rounded-full text-white flex justify-center items-center focus:outline-none fixed right-10 bottom-10 "
                    @click="showAddingGuardian">
                <AddIcon></AddIcon>
            </button>
            <div class="font-khmer_os h-screen flex justify-center items-center relative"
                 v-if="guardians && guardians.length <= 0">
                <div class="text-center relative -top-20">
                    <img src="/icon/Empty/Empty.svg" class="w-64  mb-5"/>
                </div>
            </div>
            <div v-if="guardians && guardians.length">
                <div class="grid grid-cols-3 gap-4">
                    <div v-for="(guardian, key) in guardians" :key="key">
                        <div class="flex font-khmer_os text-14px border border-gray-300 rounded-xl p-3 relative">
                            <div class="w-16 h-16 rounded-full mr-3 "
                                 :style="{backgroundImage:`url(${guardian.photo})`,backgroundSize:`cover`}"></div>
                            <div>
                                <div>ឈ្មោះ {{guardian.first_name + " " + guardian.last_name}}</div>
                                <div>លេខទូរស័ព្ទ {{guardian.phone}}</div>
                                <div>ត្រូវជា {{guardian.type}}</div>
                            </div>
                            <div class="absolute right-5 mt-5 cursor-pointer" @click="confirmDelete(guardian._id)">
                                <DeleteIcon></DeleteIcon>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
        <AddGuardian v-if="showGuardian" @closeAdd="closeAdd" @GuardianAdded="GuardianAdded"></AddGuardian>
        <Message v-if="err" :message="message" @close="close" @deleteFavorite="deleteGuardian"></Message>
    </div>
</template>
<script>
    import {mapState, mapActions} from "vuex"
    import AddIcon from "./../../components/AddIcon"
    import AddGuardian from "./components/AddGuardian.vue"
    import DeleteIcon from "./../MyCourse/components/DeleteIcon"
    import Message from "./../Favorite/components/Message"
    import Loading from "./../../components/Loading"
    export default{
        name: "Guardian",
        components: {
            AddIcon,
            AddGuardian,
            DeleteIcon,
            Message,
            Loading
        },
        data(){
            return {
                showGuardian: false,
                guardianId: "",
                err: false,
                message: ""
            }
        },
        computed: {
            ...mapState('guardian', ['guardians', 'loadingGuardian'])
        },
        methods: {
            ...mapActions('guardian', ['getGuardian', 'addingGuardian', 'removeGuardian']),
            showAddingGuardian(){
                this.showGuardian = true
            },
            closeAdd(){
                this.showGuardian = false
            },
            GuardianAdded(){
                this.showGuardian = false
                this.getGuardian()
            },
            confirmDelete(id){
                this.guardianId = id
                this.err = true
                this.message = "តើអ្នកចង់លុបអាណាព្យាបាលនេះមែនទេ?"
            },
            close(){
                this.err = false
            },
            deleteGuardian(){
                this.removeGuardian(this.guardianId).then(() => {
                    this.err = false
                })
            }
        },
        created(){
            this.getGuardian()
        }
    }
</script>