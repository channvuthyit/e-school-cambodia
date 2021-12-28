<template>
    <div id="app" class="relative">
        <div v-if="$route.name == 'login' || $route.name == 'change-password'  ">
            <router-view/>
        </div>
        <div class="flex  max-h-screen" v-else>
            <div class="border-r">
                <sidebar :stProfile="stProfile"/>
            </div>
            <div class="flex w-full ">
                <div class="border border-b-0 border-l-0 border-gray-200 w-full">
                    <Menu/>
                    <div class="flex justify-between items-center px-5 bg-white"
                         :class="$route.name !=='change-profile' && $route.name !=='about' &&
                          $route.name !=='lock' && $route.name !=='discuss'&& $route.name !=='test'
                           && $route.name !=='term' && $route.name !=='guardian'
                            && $route.name !=='insurance' && $route.name !=='invoice' && $route.name !=='course-detail'
                             && $route.name !=='help'  && $route.name !=='partner'
                             && $route.name !=='download'  && $route.name !=='download-detail'
                           && $route.name !=='other'?'border border-t-0 border-r-0 border-l-0 border-gray-200 ':''">
                        <Search/>
                        <div>
                            <FilterClass/>
                        </div>
                    </div>
                    <div>
                        <router-view/>
                    </div>
                </div>
            </div>
        </div>
        <UpdateVersion v-if="showUpdate" @dismiss="dismiss"
                       @updateVersion="updateVersion"></UpdateVersion>
        <DownloadingNewVersion v-if="showDownloading"></DownloadingNewVersion>
    </div>
</template>
<script>
    import Sidebar from "./views/Sidebar/Sidebar.vue";
    import Menu from "./views/Menu/Menu.vue";
    import Search from "./views/Search/Search.vue";
    import FilterClass from "./views/Filter/FilterClass.vue";
    import {mapState, mapActions} from "vuex"
    import axios from 'axios'
    import config from "./config"
    import UpdateVersion from "./components/UpdateVersion"
    import DownloadingNewVersion from "./components/DownloadingNewVersion"
    import MenuIcon from "./components/MenuIcon.vue"
    const {ipcRenderer} = require('electron')


    export default {
        components: {
            Sidebar,
            Menu, Search, FilterClass,
            UpdateVersion,
            DownloadingNewVersion,
            MenuIcon
        },
        data(){
            return {
                showUpdate: false,
                showDownloading: false,
            }
        },

        computed: {
            ...mapState('auth', ['stProfile']),
        },

        methods: {
            ...mapActions('auth', ['getStudentProfile']),
            ...mapActions('setting', ['checkingAppVersion']),

            dismiss(){
                this.showUpdate = false
            },

            updateVersion(){
                this.showUpdate = false
                ipcRenderer.send("updateVersion", this.version)
            }
        },
        mounted(){
            if (localStorage.getItem("stProfile") !== undefined || localStorage.getItem("stProfile") !== null) {
                axios.get(config.checkingVersionUrl+"update?os=mac&version="+config.appVersion).then(res => {
                    if (res.data.data.is_update ===1 ) {
                        this.showUpdate = true
                        
                    }
                })

            }
        },
        created(){
            if (!this.stProfile.first_name && !this.stProfile.last_name) {
                if (JSON.parse(localStorage.getItem('stProfile'))) {
                    this.getStudentProfile(JSON.parse(localStorage.getItem('stProfile')))
                }
            }
            ipcRenderer.on("checking-for-update", () => {
                this.showDownloading = true
            })

        }
    };
</script>
<style lang="scss">
    body {
        overflow-x: hidden;
    }
</style>

