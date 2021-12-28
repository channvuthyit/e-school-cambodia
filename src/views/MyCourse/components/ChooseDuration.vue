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
                        <div class="mt-2 py-2 w-60">
                            <div class="border border-gray-200 border-t-0 border-l-0 border-r-0 flex justify-center items-center">
                                <p class="text-base text-black  font-khmer_os mb-3">
                                    {{ message }}
                                </p>
                            </div>
                            <ul class="list-none font-khmer_os text-primary">
                                <li class="border border-t-0 py-2 border-l-0 border-r-0 border-gray-200 text-center cursor-pointer hover:bg-gray-50"
                                    @click="chooseDuration(1)" :class="duration === 1?'text-red-500':'text-black'">1 ខែ
                                </li>
                                <li class="border border-t-0 py-2 border-l-0 border-r-0 border-gray-200 text-center cursor-pointer hover:bg-gray-50"
                                    @click="chooseDuration(2)" :class="duration === 2?'text-red-500':'text-black'">2 ខែ
                                </li>
                                <li class="border border-t-0 py-2 border-l-0 border-r-0 border-gray-200 text-center cursor-pointer hover:bg-gray-50"
                                    @click="chooseDuration(3)" :class="duration === 3?'text-red-500':'text-black'">3 ខែ
                                </li>
                                <li class="border border-t-0 py-2 border-l-0 border-r-0 border-gray-200 text-center cursor-pointer hover:bg-gray-50"
                                    @click="chooseDuration(6)" :class="duration === 6?'text-red-500':'text-black'">6 ខែ
                                </li>
                                <li class="py-1 text-center cursor-pointer hover:bg-gray-50"
                                    @click="chooseDuration(12)" :class="duration === 12?'text-red-500':'text-black'">1 ឆ្នាំ
                                </li>
                            </ul>
                        </div>
                    </div>
                </div>
                <hr>
                <div class="sm:flex sm:flex-row text-base font-khmer_os py-3 justify-center items-center">
                    <button type="button" @click="closeDuration"
                            class="w-full inline-flex justify-center  px-4   text-base font-medium text-primary focus:outline-none sm:ml-3 sm:w-auto sm:text-base">
                        បោះបង់
                    </button>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
    export default{
        name: "ChooseDuration",
        props: {
            message: {
                type: String,
                default: () => {
                    return 'សូមជ្រើសរើសរយៈពេល'
                }
            },
            size: {
                type: Number,
                default: () => {
                    return 60;
                }
            },
            duration: {
                type: Number,
                default: () => {
                    return 12
                }
            }
        },
        methods: {
            closeDuration($event){
                this.$emit("closeDuration", $event);
            },

            chooseDuration($event){
                this.$emit("choseDuration", $event);
            }
        }
    }
</script>