<template>
    <div class="fixed inset-0 overflow-y-auto" style="z-index: 52">
        <div class="flex items-end justify-center min-h-screen text-center sm:block sm:p-0">
            <div class="fixed inset-0 transition-opacity" aria-hidden="true">
                <div class="absolute inset-0 bg-gray-500 opacity-75"></div>
            </div>
            <span class="hidden sm:inline-block sm:align-middle sm:h-screen" aria-hidden="true">&#8203;</span>
            <div class="inline-block align-bottom bg-white rounded-xl text-left overflow-hidden shadow-xl transform transition-all  sm:align-middle"
                 :class="`w-${size}`" role="dialog" aria-modal="true" aria-labelledby="modal-headline">
                <div class="bg-white">
                    <div class="flex justify-center items-center">
                        <div class="text-center mt-0 mx-4 ">
                            <div class="mt-2 py-4">
                                <p class="text-base text-base font-khmer_os">
                                    {{ message }}
                                </p>
                            </div>
                        </div>
                    </div>
                </div>
                <hr>
                <div class="sm:flex sm:flex-row text-base font-khmer_os h-10 justify-center items-center">
                    <button type="button" @click="closeMessage"
                            v-if="!hide"
                            class="w-full inline-flex justify-center  px-4   text-base font-medium text-primary focus:outline-none sm:ml-3 sm:w-auto sm:text-base">
                        បោះបង់
                    </button>
                    <div v-if="!hide" class="h-10 border border-gray-300 border-l-0 border-t-0 border-b-0"
                         style="width:0px;"></div>
                    <button type="button" @click="showCart"
                            class="w-full inline-flex justify-center  px-4   text-base font-medium text-primary focus:outline-none sm:ml-3 sm:w-auto sm:text-base">
                        បាទ/ចាស់
                    </button>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
    export default{
        name: "Message",
        props: {
            message: {
                type: String,
                default: () => {
                    return 'សូមធ្វើការទិញថ្នាក់នេះជាមុនសិន'
                }
            },
            size: {
                type: Number,
                default: () => {
                    return 60;
                }
            },
            hide: {
                type: Boolean,
                default: function () {
                    return false
                }
            },
        },
        methods: {
            closeMessage($event){
                this.$emit("closeMessage", $event);
            },

            showCart($event){
                this.$emit("showCart", $event);
            }
        }
    }
</script>