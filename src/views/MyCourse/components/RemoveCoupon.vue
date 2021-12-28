<template>
    <div class="fixed z-50 inset-0 overflow-y-auto">
        <div class="flex items-end justify-center min-h-screen text-center sm:block sm:p-0">
            <div class="fixed inset-0 transition-opacity" aria-hidden="true">
                <div class="absolute inset-0 bg-gray-500 opacity-75"></div>
            </div>
            <span class="hidden sm:inline-block sm:align-middle sm:h-screen" aria-hidden="true">&#8203;</span>
            <div class="inline-block align-bottom bg-white rounded-xl text-left overflow-hidden shadow-xl transform transition-all  sm:align-middle"
                 :class="`w-${size}`" role="dialog" aria-modal="true" aria-labelledby="modal-headline">
                <div class="bg-white">
                    <div class="flex justify-center items-center">
                        <div class="text-center">
                            <div class="mt-2 py-4">
                                <p class="text-base text-black font-khmer_os">
                                    {{ message }}
                                </p>
                            </div>
                        </div>
                    </div>
                </div>
                <hr>
                <div class="sm:flex sm:flex-row text-base font-khmer_os h-10 justify-center items-center">
                    <button type="button" @click="closeRemoveCoupon"
                            class="w-full inline-flex justify-center  px-4   text-base font-medium text-primary focus:outline-none sm:ml-3 sm:w-auto sm:text-base">
                        បោះបង់
                    </button>
                    <div class="h-10 border border-gray-300 border-l-0 border-t-0 border-b-0" style="width:0px;"></div>
                    <button type="button" @click="ConfirmDeleteCoupon"
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
        name: "ConfirmDelete",
        props: {
            message: {
                type: String,
                default: () => {
                    return 'តើអ្នកចង់លុបលេខគូប៉ុងនេះមែនទេ?'
                }
            },
            size: {
                type: Number,
                default: () => {
                    return 60;
                }
            }
        },
        methods: {
            closeRemoveCoupon($event){
                this.$emit("closeRemoveCoupon", $event);
            },

            ConfirmDeleteCoupon($event){
                this.$emit("ConfirmDeleteCoupon", $event);
            }
        }
    }
</script>