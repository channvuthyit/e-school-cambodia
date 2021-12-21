<template>
    <div class="font-khmer_os bg-white h-screen">
        <div class="p-5">
            <div class=" flex items-center justify-center items-center">
                <img :src="stProfile.photo" class="rounded-full h-24 w-24 object-cover shadow">
            </div>
            <div class="text-center">
                <h1 class="mt-3 text-lg font-semibold text-gray-700">
                    {{stProfile.first_name}} {{stProfile.last_name}}</h1>
            </div>
        </div>
        <hr>
        <div class="py-0 px-3 mt-3 pb-3">
            <ul class="list-outside bg-rose-200 text-left mt-5 leading-8 font-khmer_os text-base">
                <li class="flex items-center hover:text-custom pointer cursor-pointer"
                    :class="$route.name == 'my-course'?'text-custom':''"
                    @mouseover="icons.course.original = icons.course.hover"
                    @mouseout="icons.course.original = icons.course.out"
                    @click="goTo('my-course')"
                >
                    <YourCourseIcon
                            :fill="$route.name == 'my-course'?icons.course.hover:icons.course.original"></YourCourseIcon>
                    <span class="ml-2">ថ្នាក់សិក្សា</span>
                </li>
                <li class="flex items-center hover:text-custom pointer cursor-pointer"
                    :class="$route.name == 'download'?'text-custom':''"
                    @mouseover="icons.download.original = icons.download.hover"
                    @mouseout="icons.download.original = icons.download.out"
                    @click="goTo('download')"
                >
                    <DownloadIcon
                            :fill="$route.name == 'download'?icons.download.hover:icons.download.original"></DownloadIcon>
                    <span class="ml-2">ឯកសារទាញយក</span>
                </li>
                <li class="flex items-center hover:text-custom pointer cursor-pointer"
                    :class="$route.name == 'favorite'?'text-custom':''"
                    @mouseover="icons.favorite.original = icons.favorite.hover"
                    @mouseout="icons.favorite.original = icons.favorite.out"
                    @click="goTo('favorite')"
                >
                    <FavoriteIcon
                            :fill="$route.name == 'favorite'?icons.favorite.hover:icons.favorite.original"></FavoriteIcon>
                    <span class="ml-2"> វីដេអូចូលចិត្ត</span>
                </li>
            </ul>
        </div>
        <hr>
        <div class="py-0 px-3 mt-3 pb-3">
            <ul class="list-outside bg-rose-200 text-left leading-8 font-khmer_os  text-base">
                <li class="flex items-center hover:text-custom pointer cursor-pointer"
                    @click="goTo('change-profile')"
                    :class="$route.name == 'change-profile'?'text-custom':''"
                    @mouseover="icons.change_profile.original = icons.change_profile.hover"
                    @mouseout="icons.change_profile.original = icons.change_profile.out"
                >
                    <UserIcon
                            :fill="$route.name == 'change-profile'?icons.change_profile.hover:icons.change_profile.original"></UserIcon>
                    <span class="ml-2">ព័ត៌មានផ្ទាល់ខ្លួន</span>
                </li>
                <li class="flex items-center hover:text-custom pointer cursor-pointer"
                    :class="$route.name == 'lock'?'text-custom':''"
                    @mouseover="icons.lock.original = icons.lock.hover"
                    @mouseout="icons.lock.original = icons.lock.out"
                    @click="goTo('lock')"
                >
                    <LockIcon
                            :fill="$route.name == 'lock'?icons.lock.hover:icons.lock.original"></LockIcon>
                    <span class="ml-2">កែប្រែពាក្យសម្ងាត់</span>
                </li>
                <li class="flex items-center hover:text-custom pointer cursor-pointer"
                    @click="goTo('guardian')"
                    :class="$route.name == 'guardian'?'text-custom':''"
                    @mouseover="icons.guardian.original = icons.guardian.hover"
                    @mouseout="icons.guardian.original = icons.guardian.out"
                >
                    <GuardianIcon
                            :fill="$route.name == 'guardian'?icons.guardian.hover:icons.guardian.original"></GuardianIcon>
                    <span class="ml-2">អាណាព្យាបាល</span>
                </li>
                <li class="flex items-center hover:text-custom pointer cursor-pointer"
                    @click="goTo('insurance')"
                    :class="$route.name == 'insurance'?'text-custom':''"
                    @mouseover="icons.insurance.original = icons.insurance.hover"
                    @mouseout="icons.insurance.original = icons.insurance.out"
                >
                    <InsuranceIcon
                            :fill="$route.name == 'insurance'?icons.insurance.hover:icons.insurance.original"></InsuranceIcon>
                    <span class="ml-2">ធានារ៉ាប់រង</span>
                </li>
                <li class="flex items-center hover:text-custom pointer cursor-pointer"
                    @click="goTo('invoice')"
                    :class="$route.name == 'invoice'?'text-custom':''"
                    @mouseover="icons.invoice.original = icons.invoice.hover"
                    @mouseout="icons.invoice.original = icons.invoice.out"
                >
                    <InvoiceIcon
                            :fill="$route.name == 'invoice'?icons.invoice.hover:icons.invoice.original"></InvoiceIcon>
                    <span class="ml-2">វិក្កយបត្រ</span>
                </li>
                <li class="flex items-center hover:text-custom pointer cursor-pointer"
                    @click="goTo('other')"
                    :class="$route.name == 'other'?'text-custom':''"
                    @mouseover="icons.other.original = icons.other.hover"
                    @mouseout="icons.other.original = icons.other.out"
                >
                    <OtherIcon :fill="$route.name == 'other'?icons.other.hover:icons.other.original"></OtherIcon>
                    <span class="ml-2">ផ្សេងៗ</span>
                </li>
            </ul>
        </div>
        <hr>
        <div class="py-0 px-3 mt-3">
            <ul class="list-outside bg-rose-200 text-left mt-5 leading-8 font-khmer_os  text-base">
                <li class="flex items-center hover:text-custom pointer cursor-pointer"
                    @click="goTo('term')"
                    :class="$route.name == 'term'?'text-custom':''"
                    @mouseover="icons.term.original = icons.term.hover"
                    @mouseout="icons.term.original = icons.term.out"
                >
                    <TermAndConditionIcon
                            :fill="$route.name == 'term'?icons.term.hover:icons.term.original"></TermAndConditionIcon>
                    <span class="ml-2">លក្ខណ្ឌនៃការប្រើប្រាស់</span>
                </li>

                <li class="flex items-center hover:text-custom pointer cursor-pointer"
                    @click="goTo('help')"
                    :class="$route.name == 'help'?'text-custom':''"
                    @mouseover="icons.help.original = icons.help.hover"
                    @mouseout="icons.help.original = icons.help.out"
                >
                    <HelpIcon :fill="$route.name == 'help'?icons.help.hover:icons.help.original"></HelpIcon>
                    <span class="ml-2">ជំនួយបន្ថែម</span>
                </li>
                <li class="flex items-center hover:text-custom pointer cursor-pointer"
                    :class="$route.name == 'about'?'text-custom':''"
                    @click="goTo('about')"
                    @mouseover="icons.about.original = icons.about.hover"
                    @mouseout="icons.about.original = icons.about.out"
                >
                    <InfoIcon :fill="$route.name == 'about'?icons.about.hover:icons.about.original"></InfoIcon>
                    <span class="ml-2">អំពីយើង</span>
                </li>
            </ul>
        </div>
        <div class="absolute bottom-0 w-full text-white cursor-pointer" @click="userLogout">
            <div class="font-khmer_os w-full p-3 bg-custom hover:bg-opacity-90 text-base text-white text-center flex justify-center">
                <LogoutIcon></LogoutIcon>
                <button class="focus:outline-none ml-2">ចាកចេញ</button>
            </div>
        </div>
    </div>
</template>

<script>
    import FavoriteIcon from "./../../components/FavoriteIcon"
    import InvoiceIcon from "./../../components/InvoiceIcon"
    import InsuranceIcon from "./../../components/InsuranceIcon"
    import LogoutIcon from "./../../components/LogoutIcon"
    import LockIcon from "./../../components/LockIcon"
    import YourCourseIcon from "./../../components/YourCourseIcon"
    import UserIcon from "./../../components/UserIcon"
    import OtherIcon from "./../../components/OtherIcon"
    import DownloadIcon from "./../../components/DownloadIcon"
    import InfoIcon from "./../../components/InfoIcon"
    import HelpIcon from "./../../components/HelpIcon"
    import {mapActions} from "vuex"
    import TermAndConditionIcon from "./../../components/TermIcon"
    import GuardianIcon from "./../../components/GuardianIcon"

    export default{
        name: "Sidebar",
        components: {
            InvoiceIcon,
            FavoriteIcon,
            LockIcon,
            YourCourseIcon,
            UserIcon,
            OtherIcon,
            InfoIcon,
            HelpIcon,
            LogoutIcon,
            TermAndConditionIcon,
            GuardianIcon,
            InsuranceIcon,
            DownloadIcon
        },
        props: {
            stProfile: {
                type: Object,
                default: function () {
                    return {
                        _id: null,
                        last_name: null,
                        first_name: null,
                        phone: null,
                        gender: null,
                        group_type: null,
                        grade_id: null,
                        province: {
                            _id: null,
                            name: null
                        },
                        school: {
                            _id: null,
                            name: null
                        },
                        photo: ""
                    }
                }
            }
        },
        data(){
            return {
                icons: {
                    home: {
                        original: '#000000',
                        out: '#000000',
                        hover: '#00a0e4'
                    },
                    course: {
                        original: '#000000',
                        out: '#000000',
                        hover: '#00a0e4'
                    },
                    lock: {
                        original: '#6f6f6f',
                        out: '#6f6f6f',
                        hover: '#00a0e4'
                    },
                    favorite: {
                        original: '#000000',
                        out: '#000000',
                        hover: '#00a0e4'
                    },
                    other: {
                        original: '#000000',
                        out: '#000000',
                        hover: '#00a0e4'
                    },
                    help: {
                        original: '#000000',
                        out: '#000000',
                        hover: '#00a0e4'
                    },
                    change_profile: {
                        original: '#000000',
                        out: '#000000',
                        hover: '#00a0e4'
                    },
                    about: {
                        original: '#000000',
                        out: '#000000',
                        hover: '#00a0e4'
                    },
                    term: {
                        original: '#000000',
                        out: '#000000',
                        hover: '#00a0e4'
                    },
                    guardian: {
                        original: '#000000',
                        out: '#000000',
                        hover: '#00a0e4'
                    },
                    insurance: {
                        original: '#000000',
                        out: '#000000',
                        hover: '#00a0e4'
                    },
                    invoice: {
                        original: '#000000',
                        out: '#000000',
                        hover: '#00a0e4'
                    },
                    download: {
                        original: '#000000',
                        out: '#000000',
                        hover: '#00a0e4'
                    }
                }
            }
        },

        methods: {
            ...
                mapActions('auth', ['logout']),
            goTo(route)
            {
                if (route !== this.$route.name) {
                    this.$router.push({name: `${route}`})
                }
            },
            redirectTo(){
                localStorage.removeItem('token');
                localStorage.removeItem('stProfile');
                localStorage.removeItem('provinces');

                this.$router.push({
                    name: 'login'
                })
            },
            userLogout()
            {
                this.logout().then(() => {
                    this.redirectTo()
                }).catch(() => {
                    this.redirectTo()
                })

            }
        }
    }
</script>