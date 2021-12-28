<template>
    <div class="fixed z-10 inset-0 overflow-y-auto">
        <div class="flex items-end justify-center min-h-screen text-center sm:block sm:p-0">
            <div class="fixed inset-0 transition-opacity" aria-hidden="true">
                <div class="absolute inset-0 bg-gray-500 opacity-75"></div>
            </div>
            <span class="hidden sm:inline-block sm:align-middle sm:h-screen" aria-hidden="true">&#8203;</span>
            <div class="inline-block align-bottom bg-white rounded-xl overflow-hidden shadow-xl transform transition-all  sm:align-middle"
                 :class="`w-${size}`" role="dialog" aria-modal="true" aria-labelledby="modal-headline">
                <div class="bg-white">
                    <div class="flex justify-center items-center">
                        <div class="text-center">
                            <div class="mt-2 py-3 px-3">
                                <p class="text-base text-black text-base font-khmer_os">
                                    {{message}}
                                </p>
                            </div>
                        </div>
                    </div>
                </div>
                <hr>
                <div class="flex justify-center items-center font-khmer_os py-3">
                    <button type="button" @click="closeErr"
                            class="w-full inline-flex justify-center  px-4   text-base font-medium text-primary focus:outline-none sm:ml-3 sm:w-auto sm:text-base">
                        បាទ/ចាស
                    </button>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
    export default{
        name: "ErrMessage",
        props: {
            message: {
                type: String,
                default: () => {
                    return "លេខទូរស័ព្ទមិនត្រឹមត្រូវ"
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
            closeErr($event){
                this.$emit("closeErr", $event);
            }
        }
    }
</script>