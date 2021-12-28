<template>
    <div class="bg-white h-screen py-5">
        <div class="px-5 font-khmer_os text-14px flex-cols justify-center items-center text-center w-96">
            <div class="w-24 h-24 rounded-full flex justify-center items-center m-auto cursor-pointer border border-gray-200 mb-2 relative photo"
                 :style="{backgroundSize:'cover',backgroundImage:`url(${stProfile.photo})`}" @click="changePhoto">
                <div class="camera absolute bg-gray-100 rounded-full w-8 h-8 justify-center items-center flex bottom-0 right-0">
                    <CameraIcon></CameraIcon>
                </div>
                <div class="loader" v-if="updatingPhoto">
                    <Loader></Loader>
                </div>
            </div>
            <p class="mb-5">{{"0" + stProfile.phone}}</p>
            <form>
                <input type="file" style="display: none" ref="image" name="image" @change="onFileChange"
                       accept="image/x-png,image/gif,image/jpeg">
            </form>
            <div class="flex justify-stat items-center relative">
                <div class="absolute left-0 top-0 mt-2 ml-3 opacity-40">
                    <EditUserIcon></EditUserIcon>
                </div>
                <input type="text"
                       v-model="stProfile.first_name"
                       class="border rounded border-gray-200 py-2 focus:outline-none w-96 pl-10 mb-5"
                       placeholder="នាមត្រកូល"/>
            </div>

            <div class="flex justify-stat items-center relative">
                <div class="absolute left-0 top-0 mt-2 ml-3 opacity-40">
                    <EditUserIcon></EditUserIcon>
                </div>
                <input type="text"
                       v-model="stProfile.last_name"
                       class="border rounded border-gray-200 py-2 focus:outline-none w-96 pl-10 mb-5"
                       placeholder="នាមខ្លួន"/>
            </div>

            <div class="flex justify-start items-center relative text-14px mb-5">
                <div class="absolute left-0 opacity-40">
                    <GenderIcon></GenderIcon>
                </div>
                <label class="mr-5 ml-10">
                    <input type="radio" name="gender" value="M" class="mr-2 ml-4 "
                           :checked="stProfile.gender == 'M'" @click="changeGender('M')">ប្រុស
                </label>
                <label>
                    <input type="radio" name="gender" value="F" class="mr-2"
                           :checked="stProfile.gender == 'F'" @click="changeGender('F')">ស្រី
                </label>
            </div>

            <div class="flex justify-stat items-center relative">
                <div class="absolute left-0 top-0 mt-2 opacity-30 ml-3">
                    <CalendarIcon></CalendarIcon>
                </div>
                <input type="date"
                       class="border rounded border-gray-200 py-2 focus:outline-none w-96 pl-12 mb-5"
                       placeholder="ថ្ងៃខែឆ្នាំកំណើត" v-model="stProfile.date_of_birth">
            </div>

            <div class="flex justify-stat items-center relative">
                <div class="absolute left-0 top-0 mt-1 ml-3 opacity-30">
                    <MapIcon></MapIcon>
                </div>
                <button class="border rounded border-gray-200 py-2 focus:outline-none w-96 pl-12 mb-5 text-left"
                        @click="showAllProvince">
                    {{stProfile.province.name}}
                </button>
                <img src="/ajax-loader.gif" class="absolute left-96 top-0 mt-2" v-if="loadingProvince"/>
            </div>
            <div class="flex justify-stat items-center relative">
                <div class="absolute left-0 top-0 mt-1 ml-3 opacity-30">
                    <UniversityIcon></UniversityIcon>
                </div>
                <button class="border rounded border-gray-200 py-2 focus:outline-none w-96 pl-12 mb-5 text-left h-10"
                        @click="showAllSchool">
                    {{stProfile.school.name}}
                </button>

                <img src="/ajax-loader.gif" class="absolute left-80 top-0 mt-3" v-if="loadingSchool"/>
            </div>
            <div class="flex justify-start items-center mt-5">
                <button @click="updateProfile"
                        :disabled="loading"
                        class="text-14px flex justify-between items-center bg-custom h-11 px-3 rounded text-white focus:outline-none :hover-opacity-50 hover:bg-opacity-80">
                    <SaveIcon></SaveIcon>
                    <span class="pl-1">កែប្រែពត៍មាន</span>
                    <span v-if="loading">&nbsp;</span>
                    <Loader v-if="loading" :size="10"></Loader>
                </button>
            </div>
            <Message v-if="err" :message="errMessage" @closeMessage="closeMessage"></Message>
            <Province v-if="showProvince" :provinces="provinces" @selectProvince="selectProvince"
                      @closeProvince="closeProvince"></Province>
            <School v-if="showSchool" :schools="schools" @selectSchool="selectSchool"
                    @closeSchool="closeSchool"></School>
        </div>
    </div>
</template>

<script>
    import EditUserIcon from "./../../components/EditUserIcon"
    import CalendarIcon from "./../../components/CalendarIcon"
    import GenderIcon from "./../../components/GenderIcon"
    import MapIcon from "./../../components/MapIcon"
    import {mapActions, mapState} from "vuex"
    import UniversityIcon from "./../../components/UniversityIcon"
    import SaveIcon from "./../../components/SaveIcon"
    import CameraIcon from "./../../components/CameraIcon"
    import Message from "./components/Message.vue"
    import Loader from "./../../components/Loader"
    import Province from "./components/Province"
    import School from "./components/School"

    export default{
        name: "Profile",
        components: {
            CameraIcon,
            EditUserIcon,
            CalendarIcon,
            GenderIcon,
            MapIcon,
            UniversityIcon,
            SaveIcon,
            Message,
            Loader,
            Province,
            School
        },
        data(){
            return {
                image: null,
                err: false,
                errMessage: null,
                loading: false,
                showProvince: false,
                showSchool: false,
            }
        },
        computed: {
            ...mapState('setting', ['provinces', 'schools', 'loadingProvince', 'loadingSchool']),
            ...mapState('auth', ['stProfile', 'updatingPhoto'])
        },
        methods: {
            ...mapActions('setting', ['getProvinces', 'getSchool']),
            ...mapActions('auth', ['changeProfile', 'getStudentProfile', 'changeProfilePhotoPhoto']),

            closeProvince(){
                this.showProvince = false
            },

            closeSchool(){
                this.showSchool = false
            },

            changePhoto(){
                this.$refs.image.click()
            },

            showAllProvince(){
                this.stProfile.school.name = null
                this.showProvince = true
            },
            updateProfile(){
                if (!this.stProfile.first_name) {
                    this.err = true
                    this.errMessage = "សូមបញ្ចូលនាមត្រកូល"
                    return false
                }
                if (!this.stProfile.last_name) {
                    this.err = true
                    this.errMessage = "សូមបញ្ចូលនាមខ្លួន"
                    return false
                }
                if (!this.stProfile.province) {
                    this.err = true
                    this.errMessage = "សូមជ្រើសរើសខេត្ត"
                    return false
                }
                if (!this.stProfile.first_name) {
                    this.err = true
                    this.errMessage = "សូមជ្រើរើសសាលា"
                    return false
                }

                this.loading = true

                let user = {
                    "last_name": this.stProfile.last_name,
                    "first_name": this.stProfile.first_name,
                    "gender": this.stProfile.gender,
                    "date_of_birth": this.stProfile.date_of_birth,
                    "school": {
                        _id: this.stProfile.school._id,
                        name: this.stProfile.school.name
                    },
                    "province": {
                        _id: this.stProfile.province._id,
                        name: this.stProfile.province.name
                    }
                }

                this.changeProfile(user).then(() => {
                    this.loading = false
                    this.err = true
                    this.errMessage = "ពត៍មានត្រូវបានកែប្រែជោគជ័យ"

                    this.getStudentProfile(this.stProfile)
                    localStorage.setItem('stProfile', JSON.stringify(this.stProfile))
                }).catch(() => {
                    this.err = true
                    this.errMessage = "ការកែប្រែពត៍មានត្រូវបានបរាជ័យ"
                    this.loading = false
                })

            },
            closeMessage()
            {
                this.err = false
            },

            changeGender(gender){
                this.stProfile.gender = gender
            },
            selectProvince(province)
            {
                this.getSchool(province._id).then(() => {
                    this.showProvince = false
                    this.stProfile.province = province
                })
            }
            ,
            showAllSchool()
            {
                if (!this.schools.length || this.loadingSchool) {
                    return false
                }
                this.showSchool = true
            }
            ,

            selectSchool(school)
            {

                this.showSchool = false
                this.stProfile.school = school

            }
            ,

            onFileChange(e)
            {
                const file = e.target.files[0];
                this.stProfile.photo = URL.createObjectURL(file);
                this.image = this.$refs.image.files[0];

                let formData = new FormData();

                formData.append("image", e.target.files[0]);

                this.changeProfilePhotoPhoto(formData).then(response => {
                    this.stProfile.photo = response.data.photo
                    this.getStudentProfile(this.stProfile)
                    localStorage.setItem('stProfile', JSON.stringify(this.stProfile))
                }).catch(() => {
                    console.log('err')
                })
            }
        },

        created(){
            this.getProvinces()
        }
    }
</script>

<style>
    .camera {
        visibility: hidden;
    }

    .photo:hover .camera {
        transition: 0.5s linear;
        visibility: visible;
    }
</style>