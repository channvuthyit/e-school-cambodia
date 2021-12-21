<template>
    <div class="fixed z-10 inset-0 overflow-y-auto">
        <div class="flex items-end justify-center min-h-screen text-center sm:block sm:p-0">
            <div class="fixed inset-0 transition-opacity" aria-hidden="true">
                <div class="absolute inset-0 bg-black opacity-95"></div>
            </div>
            <span class="hidden sm:inline-block sm:align-middle sm:h-screen" aria-hidden="true">&#8203;</span>
            <div class="w-2/5 inline-block align-bottom bg-white rounded-xl overflow-hidden shadow-xl transform transition-all  sm:align-middle px-5"
                 role="dialog" aria-modal="true" aria-labelledby="modal-headline">
                <div class="bg-white" @click="closeTermAndCondition">
                    <div v-if="loadingTerm" class="p-5">
                        <img src="ajax-loader.gif" class="m-auto"/>
                    </div>
                    <div v-else style="max-height: 80vh; overflow-y: scroll">
                        <div v-html="termAndCondition" @click="closeTermAndCondition"></div>
                    </div>
                </div>
                <hr>
            </div>
        </div>
    </div>
</template>

<script>
    import {mapActions, mapState} from "vuex"

    export default{
        name: "TermAndCondition",
        props: {
            size: {
                type: Number,
                default: () => {
                    return 96;
                }
            }
        },

        computed: {
            ...mapState('view', ['termAndCondition', 'loadingTerm'])
        },

        created(){
            this.getTerm()
        },

        methods: {
            ...mapActions('view', ['getTerm']),

            closeTermAndCondition(e){
                e.preventDefault()
                if (e.target.tagName === "A") {
                    this.$emit("closeTermAndCondition")
                }
            }
        }
    }
</script>

<style scoped>
    body {
        scrollbar-3dlight-color: #000000;
        scrollbar-arrow-color: #ffffff;
        scrollbar-darkshadow-color: #000000;
        scrollbar-face-color: #000000;
        scrollbar-highlight-color: #ffffff;
        scrollbar-shadow-color: #ffffff;
        scrollbar-track-color: #000000;
    }
</style>