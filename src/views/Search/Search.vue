<template>
    <div class="flex py-3" style="margin-bottom: 1px;"
         v-if="$route.name !=='change-profile' && $route.name !=='about' && $route.name !=='lock'
         && $route.name !=='discuss'&& $route.name !=='test'&& $route.name !=='other'
         && $route.name !=='help' && $route.name !=='term' && $route.name !=='guardian'
         && $route.name !=='partner'  && $route.name !=='download'  && $route.name !=='download-detail'
         && $route.name !=='insurance' && $route.name !=='invoice' && $route.name !=='course-detail'">
        <input type="text" placeholder="ស្វែងរកមេរៀន"
               v-on:keyup.enter="setQueryString"
               v-model="s"
               class="border border-gray-400 h-9 w-80 96-3 font-khmer_os focus:outline-none border pl-3 border-r-0 text-base rounded rounded-r-none">
        <div class="border border-gray-400 bg-gray-50 px-5 cursor-pointer flex  justify-center items-center rounded-l-none rounded"
             @click="setQueryString">
            <SearchIcon/>
        </div>
    </div>
</template>

<script>
    import SearchIcon from "./../../components/SearchIcon.vue"
    import {mapActions} from "vuex"
    export default {
        components: {
            SearchIcon
        },
        data(){
            return {
                s: ''
            }
        },
        methods: {
            ...mapActions('course', ['filterByQueryString']),

            setQueryString(){
                this.filterByQueryString(this.s)
            }
        }
    }
</script>

<style>

</style>
