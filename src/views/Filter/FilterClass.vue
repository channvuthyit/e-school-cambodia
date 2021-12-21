<template>
    <div class="w-110 pb-1"
         v-if="$route.name !=='change-profile' && $route.name !=='about'
         && $route.name !=='lock' && $route.name !=='discuss'&& $route.name !=='test'
         && $route.name !=='other'  && $route.name !=='help' && $route.name !=='term'
         && $route.name !=='partner'  && $route.name !=='download'  && $route.name !=='download-detail'
         && $route.name !=='guardian' && $route.name !=='insurance' && $route.name !=='invoice' && $route.name !=='course-detail'">
        <vue-horizontal responsive>
            <section style="white-space: nowrap; width: auto">
            <div class="text-center cursor-pointer flex-cols justify-center items-center">
                    <div class="break-all text-base font-khmer_os text-center my-2  hover:bg-gray-300 rounded-full py-1 px-2 border border-gray-300"
                         :class="gradeActive === ''?'bg-gray-300':'bg-gray-100'"
                         v-if="courses && courses.grade && courses.grade.length"
                         @click="filterGradeId('')">
                        ទាំងអស់
                    </div>
                </div>
            </section>
            <section v-for="(grade, key) in courses.grade" v-bind:key="key" style="white-space: nowrap; width: auto">
                <div class="text-center cursor-pointer flex-cols justify-center items-center">
                    <div class="break-all text-base font-khmer_os text-center my-2 hover:bg-gray-300 rounded-full py-1 px-2 border border-gray-300"
                         :class="gradeActive === grade._id?'bg-gray-300':'bg-gray-100'"
                         @click="filterGradeId(grade._id)">
                        {{grade.name}}
                    </div>
                </div>
            </section>
        </vue-horizontal>
    </div>
</template>
<script>
    const {ipcRenderer} = require('electron')
    import {mapState, mapActions} from 'vuex'
    import VueHorizontal from "vue-horizontal";
    import helper from "./../../helper/helper"


    export default {
        name: "FilterClass",
        components: {
            VueHorizontal,
        },
        data() {
            return {
                gradeActive: ""
            }
        },

        computed: {
            ...mapState('course', ['courses', 'loadingVideo']),

            idOfGrade: {
                get(){
                    return this.$store.state.course.gradeID
                },
                set(gradeID){
                    return gradeID
                },

            }
        },


        methods: {
            ...mapActions('course', ['videoList', 'filterByGradeID']),
            cutString(text, limit){
                return helper.cutString(text, limit)
            },

            filterGradeId(gradeId){
                ipcRenderer.send('gradeFilter', gradeId)

                this.filterByGradeID(gradeId)
                this.gradeActive = gradeId
                this.videoList(gradeId)
            }
        },
        watch: {
            'idOfGrade': function (id) {
                this.gradeActive = id
            }
        }

    };
</script>

<style>
    #container {
        border: 1px solid #ccc;
        overflow: scroll;
        width: 200px;
    }

    #content {
        background-color: #ccc;
        width: 250px;
        white-space: nowrap;
    }

    .overflow-x-scroll::-webkit-scrollbar {
        display: none;
    }

    #container {
        width: 340px;
        height: 50px;
    }

    #list-container {
        overflow: hidden;
        width: 300px;
        float: left;
    }

    .list {
        background: grey;
        min-width: 1400px;
        float: left;
    }

    #arrowR {
        background: yellow;
        width: 20px;
        height: 50px;
        float: right;
        cursor: pointer;
    }

    #arrowL {
        background: yellow;
        width: 20px;
        height: 50px;
        float: left;
        cursor: pointer;
    }

    .item {
        background: green;
        width: 140px;
        height: 40px;
        margin: 5px;
        float: left;
        position: relative;
    }

    /* Hide scrollbar for IE, Edge and Firefox */
    .overflow-x-scroll {
        -ms-overflow-style: none; /* IE and Edge */
        scrollbar-width: none; /* Firefox */
    }

    .v-hl-responsive > *[data-v-3e0d31e2] {
        width: 100%;
        margin-right: 12px !important;
    }

    .v-hl-svg {
        width: 35px !important;
        height: 35px !important;
        margin: 6px;
        padding: 6px;
        border-radius: 0px !important;
        box-sizing: border-box;
        color: #000;
        fill: currentColor;
        box-shadow: none !important;
    }
</style>
