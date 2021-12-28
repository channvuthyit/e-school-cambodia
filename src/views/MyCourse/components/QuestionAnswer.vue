<template>
    <div>
        <div class="fixed inset-0 overflow-y-auto" style="z-index: 51" v-if="!showAnswer">
            <div class="flex items-end justify-center min-h-screen text-center sm:block sm:p-0">
                <div class="fixed inset-0 transition-opacity" aria-hidden="true">
                    <div class="absolute inset-0 bg-black opacity-75"></div>
                </div>
                <span class="hidden sm:inline-block sm:align-middle sm:h-screen" aria-hidden="true">&#8203;</span>
                <div class="inline-block align-bottom bg-white rounded-xl text-left overflow-hidden shadow-xl transform transition-all  sm:align-middle"
                     :class="`w-${size}`" role="dialog" aria-modal="true" aria-labelledby="modal-headline">
                    <div class="header flex justify-between items-center p-4 border border-gray-200 border-t-0 border-l-0 border-r-0 text-sm font-khmer_os">
                        <div class="text-left w-full font-semibold text-sm text-center">
                            {{title}}
                        </div>
                        <div class="cursor-pointer" @click="closeQuestionAndAnswer">
                            <CloseIcon :width="20" :height="20"></CloseIcon>
                        </div>
                    </div>
                    <div class="body mt-5 flex-cols justify-start items-center font-khmer_os text-13px"
                         style="height: 600px;overflow-y: scroll" @scroll="onScroll">
                        <div v-if="loadingForum"
                             class="flex justify-center items-center h-screen">
                            <div class="relative" :class="window.width <= '1366'?' -top-20':' -top-56'">
                                <Loading/>
                            </div>
                        </div>
                        <div v-else>
                            <!--Box comment-->
                            <div class="flex px-5">
                                <div class="h-10 w-10 shadow rounded-full"
                                     :class="stProfile?'bg-gray-300':''"
                                     :style="stProfile?{backgroundImage:`url(${stProfile.photo})`,backgroundSize:`cover`}:''">
                                </div>
                                <div class="flex pl-3 relative items-center flex-1">
                                    <textarea type="text"
                                              class="resize-none bg-gray-200 rounded-full focus:outline-none b-y-2 pl-12 pr-20 w-full h-10 pt-3"
                                              placeholder="បញ្ចេញមតិ..." v-model="text"
                                              ref="text"></textarea>
                                    <div class="absolute left-6 cursor-pointer" @click="chooseImage">
                                        <ImageIcon :size="30"></ImageIcon>
                                    </div>
                                    <div class="transform rotate-45 absolute right-4 cursor-pointer"
                                         v-if="(text || imgPreview)" @click="submit">
                                        <SendMessageIcon></SendMessageIcon>
                                    </div>
                                    <form>
                                        <input type="file" name="photo" ref="photo" class="hidden"
                                               @change="onFileChange"
                                               accept="image/x-png,image/gif,image/jpeg">
                                    </form>
                                </div>
                            </div>
                            <!--End box comment-->
                            <!--Box preview image-->
                            <div class="h-20 m-5 flex  justify-between items-start" v-if="imgPreview">
                                <img :src="imgPreview" class="max-h-full rounded w-auto">
                                <div class="bg-gray-300 rounded-full p-1 cursor-pointer hover:bg-gray-400"
                                     @click="removeImage">
                                    <CloseIcon :size="18"></CloseIcon>
                                </div>
                            </div>

                            <div v-if="forums && forums.length"
                                 @scroll="onScroll">
                                <ul class="mt-5">
                                    <li v-for="(forum, key) in forums" :key="key"
                                        class="flex-cols justify-start items-center px-4 py-2 hover:bg-gray-100 cursor-pointer border-4 border-b-0 border-l-0 border-r-0 border-gray-200"
                                        @click="showAnswerForm(forum)">
                                        <div class="flex items-center">
                                            <div class="w-10 h-10 shadow rounded-full"
                                                 :style="{backgroundImage:`url(${forum.user.photo})`,backgroundSize:`cover`}">
                                            </div>
                                            <div class="ml-3">
                                                <strong>{{forum.user.name}}</strong>
                                                <div class="text-gray-400 text-13px">{{formatDate(forum.date)}}</div>
                                            </div>
                                        </div>
                                        <div v-if="forum.content.photo" class="ml-14 mt-1">
                                            <div class="h-20 flex justify-between items-start">
                                                <img :src="forum.content.photo" class="max-h-full rounded w-auto">
                                            </div>
                                        </div>
                                        <div v-if="forum.content.text" class="ml-14 mt-1">
                                            <p class="whitespace-pre-wrap">{{forum.content.text}}</p>
                                        </div>
                                        <div class="ml-14 mt-1 flex items-end">
                                            <div class="flex">
                                                <div class="opacity-50 mr-1">
                                                    <ViewIcon></ViewIcon>
                                                </div>
                                                <div>{{forum.view ? forum.view : 0}}</div>
                                            </div>
                                            <div class="flex mx-2 items-end">
                                                <div class="opacity-50 mr-1">
                                                    <CommentIcon></CommentIcon>
                                                </div>
                                                <div>{{forum.comment ? forum.comment : 0}}</div>
                                            </div>
                                        </div>
                                    </li>
                                </ul>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
        <Answer v-if="showAnswer" @closeAnswer="closeAnswer" :title="title" :comment="comment"></Answer>
    </div>
</template>
<script>
    import {mapActions, mapState} from "vuex"
    import CloseIcon from "./../components/CloseIcon"
    import ImageIcon from "./../../../components/ImageIcon"
    import helper from "./../../../helper/helper"
    import moment from "moment"
    import Loading from "./../../../components/Loading"
    import CommentIcon from "./../../../components/CommentIcon"
    import SendMessageIcon from "./../../../components/SendMessageIcon"
    import ViewIcon from "./../../../components/ViewIcon"
    import Answer from "./Answer"
    export default{
        name: "QuestionAnswer",
        data(){
            return {
                stProfile: localStorage.getItem('stProfile') ? JSON.parse(localStorage.getItem('stProfile')) : "",
                window: {
                    width: 0,
                    height: 0
                },
                page: 1,
                imgPreview: null,
                text: "",
                photo: "",
                showAnswer: false,
                comment: {}
            }
        },
        props: {
            size: {
                type: Number,
                default: () => {
                    return 100
                }
            },
            title: {
                type: String,
                default: function () {
                    return ""
                }
            },
            lesson_id: {
                type: String,
                default: function () {
                    return ""
                }
            }
        },
        components: {
            CloseIcon,
            Loading,
            ImageIcon,
            CommentIcon,
            ViewIcon,
            Answer,
            SendMessageIcon
        },
        computed: {
            ...mapState('forum', ['forums', 'loadingForum', 'loadingForumPagination'])
        },
        destroyed() {
            window.removeEventListener('resize', this.handleResize);
        },
        methods: {
            ...mapActions('forum', ['getForum', 'addForum', 'getForumPagination']),
            showAnswerForm(comment){
                this.comment = comment
                this.showAnswer = true
            },
            closeAnswer(){
                this.showAnswer = false
            },
            handleResize() {
                this.window.width = window.innerWidth;
                this.window.height = window.innerHeight;
            },
            submit(){
                if (this.text.trim() || this.imgPreview) {

                    let formData = new FormData();
                    formData.append("lesson_id", this.lesson_id);
                    formData.append("text", this.text);
                    formData.append("photo", this.photo);
                    this.addForum(formData)

                    this.removeImage()
                    this.text = ""
                }

            },

            onScroll ({target: {scrollTop, clientHeight, scrollHeight}}) {
                if (scrollTop + clientHeight >= scrollHeight) {
                    this.page++

                    let params = {
                        lesson_id: this.lesson_id,
                        p: this.page,
                        s: ""
                    }
                    this.getForumPagination(params)
                }
            },
            removeImage(){
                this.imgPreview = ""
                this.$refs.photo.value = ""
                this.photo = ""
            },
            chooseImage(){
                this.$refs.photo.click()
            },
            onFileChange(e)
            {
                let reader = new FileReader();
                reader.readAsDataURL(e.target.files[0]);
                this.photo = e.target.files[0]
                reader.onloadend = () => {
                    this.imgPreview = reader.result;
                }
                this.$refs.text.focus()
            },
            formatDate(date){
                moment.locale('en');
                return moment(date).format('DD-MM-YYYY');
            },
            closeQuestionAndAnswer(){
                this.$emit("closeQuestionAndAnswer")
            },
            cutString(text, limit){
                return helper.cutString(text, limit)
            }
        },

        created(){
            window.addEventListener('resize', this.handleResize);
            this.handleResize();
            let params = {
                lesson_id: this.lesson_id,
                p: "",
                s: ""
            }
            this.getForum(params)

        }
    }
</script>
<style>
    textarea {
        resize: none;
    }
</style>