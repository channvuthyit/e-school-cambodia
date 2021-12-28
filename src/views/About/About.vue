<template>
    <div class="bg-white h-screen py-4">
        <div class="px-5 py-1 font-khmer_os">
            <div v-if="loading" class="flex justify-center items-center h-screen relative -top-5">
                <h1 class="text-sm font-semibold font-khmer_os relative -top-20">
                    <loading></loading>
                </h1>
            </div>
            <div class="text-14px" v-else>
                <div class="w-1/2">
                    <iframe :src="src" width="100%" height="270" frameborder="0" style="border:0"></iframe>
                    <div class="flex my-3">
                        <div class="mr-2">
                            <HomeIcon></HomeIcon>
                        </div>
                        <div class="pb-3 border border-gray-100 border-t-0 border-l-0 border-r-0 flex-1">
                            {{company.address}}
                        </div>
                    </div>
                    <div class="flex my-3">
                        <div class="mr-2 opacity-60">
                            <PhoneIcon></PhoneIcon>
                        </div>
                        <div class="pb-3 text-primary border border-gray-100 border-t-0 border-l-0 border-r-0 flex-1">
                            {{company.phone}}
                        </div>
                    </div>
                    <div class="flex my-3">
                        <div class="mr-2 opacity-50 ">
                            <WebIcon></WebIcon>
                        </div>
                        <div class="cursor-pointer pb-3 text-primary border border-gray-100 border-t-0 border-l-0 border-r-0 flex-1"
                             @click="openLink(company.website)">
                            {{company.website}}
                        </div>
                    </div>
                    <div class="flex my-2 border border-gray-100 border-t-0 border-l-0 border-r-0 pb-3">
                        <div class="opacity-60 mr-2 ">
                            <ShareIcon></ShareIcon>
                        </div>
                        <div>បណ្ដាញ</div>
                    </div>
                    <div class="mt-5 flex justify-center items-center">
                        <div class="mr-2 cursor-pointer" @click="openLink(facebookUrl)">
                            <FacebookIcon></FacebookIcon>
                        </div>
                        <div class="mr-3 cursor-pointer" @click="openLink(youtubeUrl)">
                            <YoutubeIcon></YoutubeIcon>
                        </div>
                        <div class="mr-3 cursor-pointer" @click="openLink(linkedinUrl)">
                            <InIcon></InIcon>
                        </div>
                        <div @click="openLink(instagramUrl)" class="cursor-pointer">
                            <InstagramIcon></InstagramIcon>
                        </div>
                    </div>
                    <div class="mt-5 flex justify-center items-center">
                        <div>Version: {{appVersion}}</div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
    import {mapActions} from "vuex"
    import HomeIcon from "./../../components/HomeIcon"
    import PhoneIcon from "./../../components/PhoneIcon"
    import WebIcon from "./../../components/WebIcon"
    import ShareIcon from "./../../components/ShareIcon"
    import FacebookIcon from "./../../components/FacebookIcon"
    import YoutubeIcon from "./../../components/YoutubeIcon.vue"
    import InIcon from "./../../components/InIcon.vue"
    import InstagramIcon from "./../../components/InstagramIcon.vue"
    const {ipcRenderer} = require('electron')
    import config from "./../../config"
    import Loading from  "./../../components/Loading"

    export default{
        name: "About",
        components: {
            HomeIcon,
            PhoneIcon,
            WebIcon,
            ShareIcon,
            FacebookIcon,
            YoutubeIcon,
            InIcon,
            InstagramIcon,
            Loading
        },
        data(){
            return {
                loading: false,
                company: null,
                instagramUrl: "https://www.instagram.com/eschoolcambodia/?igshid=atnyxttmibf1",
                facebookUrl: "https://facebook.com/196770961113954",
                youtubeUrl: "https://www.youtube.com/channel/UCXAVs_YRUwsnx5I-Toy-7Dg",
                linkedinUrl: "https://www.linkedin.com/in/eschoolcambodia/",
                src: ""
            }
        },
        computed: {
            appVersion(){
                return config.appVersion
            }
        },
        methods: {
            ...mapActions('setting', ['companyInfo']),
            openLink(link){
                ipcRenderer.send('openLink', link)
            }
        },
        created(){
            this.loading = true
            this.companyInfo().then(response => {
                this.loading = false
                this.company = response
                this.src = "https://maps.google.com/maps?q=" + response.latitude + "," + response.longitude + "&z=15&output=embed"
            }).catch(() => {
                this.loading = false
            })
        }
    }
</script>