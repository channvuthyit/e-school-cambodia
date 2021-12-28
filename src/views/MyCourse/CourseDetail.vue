<template>
    <div class="px-5 py-5">
        <div v-if="loadingCourseDetail"
             class="flex justify-center items-center h-screen relative -top-2">
            <h1 class="text-sm font-semibold font-khmer_os relative -top-20">
                <loading></loading>
            </h1>
        </div>
        <div v-else>
            <div class="flex">
                <div class="w-3/5 pr-5">
                    <div>
                        <MediaPlayer @onPlayerEnded="onPlayerEnded" @previousVideo="previousVideo"
                                     @lastWatchVideo="lastWatchVideo($event)" :last_watch="last_watch"
                                     @updateLastWatch="updateLastWatch($event)"
                                     @gettingResource="gettingResource($event)"></MediaPlayer>
                    </div>
                    <div class="flex justify-between">
                        <div class="mt-4 font-khmer_os">
                            <p class="text-14px font-semibold" v-if="!loadingPlay">
                                <span :title="videoTitle?videoTitle:videoPlay.title">{{videoTitle ? cutString(videoTitle, 60) : cutString(videoPlay.title, 60)}}</span>
                            </p>
                            <h2 class="text-gray-400 text-14px font-khmer_os mt-2">
                                {{courseDetail.course.teacher.name}}</h2>
                        </div>
                        <div class="mt-4" v-if="!courseDetail.is_buy">
                            <button class="flex items-center bg-custom font-khmer_os font-13px text-white hover:bg-opacity-80 px-3 py-2 focus:outline-none rounded-full"
                                    @click="buy">
                                <CartIcon :width="20" :height="20" fill="#FFF"/>
                                <span class="pl-1 font-thin">ទិញថ្នាក់នេះ</span>
                            </button>
                        </div>
                        <div class="mt-4 flex" v-if="courseDetail.is_buy">
                            <div class="flex bg-custom font-khmer_os text-13px focus:outline-none  text-white leading-8 h-8 px-3 rounded-full mr-3 hover:bg-opacity-80 cursor-pointer"
                                 @click="showMyDocument" v-if="(documents && documents.length)">
                                ឯកសារ
                            </div>
                            <div class="flex bg-custom font-khmer_os text-13px focus:outline-none  text-white leading-8 h-8 px-3 rounded-full hover:bg-opacity-80 cursor-pointer"
                                 @click="QuestionAndAnswer"
                            >
                                <span class="pl-1 font-thin">សំនួរចម្លើយ</span>
                            </div>
                        </div>
                    </div>
                </div>
                <div class="w-2/5 bg-white h-screen border border-gray-200 h-screen pb-5" id="courseList"
                     ref="courseDetail">
                    <div class="overflow-y-scroll pt-5" @scroll="onScroll" ref="feed" id="feed"
                         style="max-height: 91vh;">
                        <div class="flex-col  relative" :class="order == course.order?'my-2':'mb-1'"
                             v-for="(course, key) in courseDetail.list" :key="key">
                            <div id="vdActive" class="absolute left-0 h-full flex justify-center items-center pl-3"
                                 v-if="order == course.order">
                                <IconPlayActive/>
                            </div>
                            <div class="flex justify-center items-center cursor-pointer pl-5"
                                 :class="order == course.order?'bg-gray-300 py-2':(courseDetail.is_buy == 0 && course.free_watch ==0)?'opacity-50':' py-1'">
                                <div class="relative">
                                    <img :src="getPathFromUrl(course.thumbnail)" alt="" class="w-40 ml-2"
                                         :id="course.video_youtube"
                                         @click="nextOrder(course.order,course.video_youtube,(courseDetail.is_buy == 0 && course.free_watch ==0),course)">
                                    <input type="range" min="0" max="100" value="100" step="1"
                                           class="w-40 ml-2 percentage cursor-default absolute bottom-0 left-0"
                                           v-if="course.last_watch"
                                           :id="course._id"
                                           :style="lastWatchMark(course.last_watch.percentage)">
                                </div>
                                <div class="flex-1 font-khmer_os text-sm pl-3" :title="course.title"
                                     @click="nextOrder(course.order,course.video_youtube,(courseDetail.is_buy == 0 && course.free_watch ==0),course)">
                                    <div class="flex-cols">
                                        <p v-html="(key + 1 ) + ',' + cutString(course.title, window.width <= 1366?25:55)"></p>
                                        <div class="mt-3 text-14px text-gray-500 flex">
                                            <div class="opacity-60">
                                                <ViewIcon/>
                                            </div>
                                            <span class="pl-1">{{kFormatter(course.view)}}</span>
                                        </div>
                                    </div>
                                </div>
                                <div class="pr-3">
                                    <IconLog v-if="courseDetail.is_buy == 0 && course.free_watch ==0"/>
                                    <template v-else>
                                        <div v-if="isDownloading(course)">
                                            <DownloadingIcon></DownloadingIcon>
                                        </div>
                                        <template v-else>
                                            <div @click="downloadVideo(course)" v-if="!isDownloaded(course)">
                                                <DownloadLessonIcon></DownloadLessonIcon>
                                            </div>
                                        </template>
                                        <div v-if="isDownloaded(course)" @click="confirmRemoveDownload(course)">
                                            <CloseIcon :width="22" :height="22" fill="#DC2626"></CloseIcon>
                                        </div>

                                    </template>
                                </div>
                            </div>
                        </div>
                        <div class="h-20">
                            &nbsp;
                        </div>
                    </div>
                </div>
            </div>
        </div>
        <Message v-if="showMessage" @closeMessage="closeMessage"
                 @showCart="showCart"></Message>
        <MessageConfirm v-if="showConfim" @closeConfirm="closeConfirm" @confirmDelete="confirmDelete"></MessageConfirm>
        <PaymentMethod v-if="showPaymentForm" @closePaymentMethod="closePaymentMethod"></PaymentMethod>
        <Cart v-if="showCartForm" @closeCart="closeCart"></Cart>
        <QuestionAnswer v-if="showQAndA" :title="videoTitle?videoTitle:videoPlay.title"
                        @closeQuestionAndAnswer="closeQuestionAndAnswer" :lesson_id="lesson_id"></QuestionAnswer>
        <Document v-if="showDocument" :lessonTitle="lessonTitle" @closeReading="closeReading"
                  :documents="documents" @setLessonTile="setLessonTile($event)"></Document>
        <DownloadQuality v-if="showQualityForm" @closeQuality="closeQuality"
                         @downloadQuality="downloadQuality($event)"></DownloadQuality>

    </div>
</template>

<script>

    // Vue component
    import ViewIcon from "./../../components/ViewIcon.vue"
    import helper from "./../../helper/helper"
    import {mapActions, mapState} from "vuex"
    import IconLog from "./../../components/IconLog"
    import DownloadLessonIcon from "./../../components/DownloadLessonIcon"
    import IconPlayActive from "./../../components/IconPlayActive"
    import Message from "./components/Message.vue"
    import MessageConfirm from "./components/MessageConfirm.vue"
    import PaymentMethod from "./components/PaymentMethod"
    import Cart from "./components/Cart"
    import CartIcon from "./../../components/CartIcon.vue"
    import MediaPlayer from "./components/media/Player"
    import Loading from "./../../components/Loading"
    import DownloadingIcon from "./../../components/DownloadingIcon"
    import CloseIcon from "./../../components/CloseIcon"
    import QuestionAnswer from "./components/QuestionAnswer"
    import Document from "./components/Document"
    import DownloadQuality from "./components/DownloadQuality"
    const {ipcRenderer} = require('electron')
    import err from "./../../helper/err"
    export default{
        name: 'CourseDetail',
        components: {
            MediaPlayer,
            Loading,
            DownloadingIcon,
            ViewIcon,
            IconLog,
            IconPlayActive,
            Message,
            PaymentMethod,
            Cart,
            CartIcon,
            QuestionAnswer,
            Document,
            DownloadLessonIcon,
            DownloadQuality,
            CloseIcon,
            MessageConfirm
        },
        data() {
            return {
                last_watch: "",
                lastOrder: "",
                window: {
                    width: 0,
                    height: 0
                },
                videoPlay: {},
                videoTitle: null,
                loadingPlay: false,
                order: null,
                is_buy: false,
                showMessage: false,
                showConfim: false,
                showPaymentForm: false,
                showCartForm: false,
                showQAndA: false,
                resources: [],
                lesson_id: null,
                showDocument: false,
                lessonTitle: "",
                documents: [],
                download: null,
                showQualityForm: false,
                downloadingVideo: [],
                downloaded: [],
                removeVideo: ""
            }
        },

        computed: {
            ...mapState('course', ['courseDetail', 'loadingCourseDetail', 'teacher']),
            ...mapState('playVideo', ['video']),
            ...mapState('cart', ['cart', 'loadingCart'])
        },
        destroyed() {
            window.removeEventListener('resize', this.handleResize);
        },

        methods: {
            ...mapActions('course', ['getCourseDetail', 'lessonView', 'getCourseByScroll']),
            ...mapActions('playVideo', ['getVideoLink', 'gettingNextVideo', 'stopWatch']),
            ...mapActions('cart', ['addCart']),

            gettingResource($event){
                this.resources = $event
            },
            closeQuality(){
                this.showQualityForm = false
            },

            isDownloaded(video){

                if (this.downloaded.length) {
                    for (let i = 0; i < this.downloaded.length; i++) {
                        if (video._id === this.downloaded[i]._id) {
                            return true
                        }
                    }
                }
                return false
            },

            isDownloading(video){
                if (this.downloadingVideo.length) {
                    for (let i = 0; i < this.downloadingVideo.length; i++) {
                        if (this.downloadingVideo[i] === video._id) {
                            return true
                        }
                    }
                }
                return false
            },

            confirmRemoveDownload(video){
                this.removeVideo = video
                this.showConfim = true
            },
            closeConfirm(){
                this.showConfim = false
            },
            confirmDelete(){
                this.downloaded = this.downloaded.filter(item => item._id !== this.removeVideo._id)
                localStorage.setItem("downloaded", JSON.stringify(this.downloaded))
                this.showConfim = false
                ipcRenderer.send("removeDownload", this.removeVideo._id)
            },

            getDownloading(){
                this.downloadingVideo = localStorage.getItem("downloadingStorage") ? JSON.parse(localStorage.getItem("downloadingStorage")) : []
            },
            downloadQuality($event){
                this.download.quality = $event
                this.showQualityForm = false
                ipcRenderer.send("download", this.download)
                this.downloadingVideo.push(this.download._id)
                let downloadingStorage = [];

                if (localStorage.getItem("downloadingStorage")) {
                    downloadingStorage = JSON.parse(localStorage.getItem("downloadingStorage"))
                }
                downloadingStorage.push(this.download._id)
                localStorage.setItem("downloadingStorage", JSON.stringify(downloadingStorage))

                this.getDownloading()
            },
            downloadVideo(video){
                this.showQualityForm = true
                video.course = this.courseDetail.course
                video.course.teacher = this.teacher

                this.download = video
            },

            updateLastWatch(event){
                this.last_watch = event
            },
            lastWatchVideo(event){
                event.lesson_id = this.videoPlay._id
                this.stopWatch(event).then(() => {
                    let percentage = Math.round((event.mark * 100) / event.duration)
                    let last_watch = {
                        mark: event.mark,
                        order: this.order,
                        _id: this.videoPlay._id,
                        percentage: percentage
                    }

                    let downloaded = this.getItem("downloaded");

                    if (downloaded) {
                        downloaded = downloaded.filter(item => {

                            if (item._id === this.videoPlay._id) {
                                item.last_watch = last_watch
                            }
                            return item
                        })

                        this.setItem("downloaded", downloaded)
                    }

                    this.courseDetail.list = this.courseDetail.list.filter(item => {
                        if (item._id === event.lesson_id) {
                            item.last_watch = last_watch
                        }
                        return item
                    })
                })
            },
            setLessonTile($event){
                this.lessonTitle = $event
            },
            showMyDocument(){
                this.lessonTitle = this.documents.map(item => item.title)[0]
                this.showDocument = true
            },
            closeReading(){
                this.showDocument = false
            },
            QuestionAndAnswer(){
                this.lesson_id = this.videoPlay._id
                this.showQAndA = true
            },
            closeQuestionAndAnswer(){
                this.showQAndA = false
            },
            onScroll ({target: {scrollTop, clientHeight, scrollHeight}}) {
                this.lastOrder = (this.courseDetail.list[this.courseDetail.list.length - 1].order)

                if (scrollTop + clientHeight >= scrollHeight) {
                    this.getCourseByScroll({
                        courseId: this.$route.params.courseId,
                        order: this.lastOrder
                    })
                }
            },

            handleResize() {
                this.window.width = window.innerWidth;
                this.window.height = window.innerHeight;
            },
            isDownloadedExist(video){
                let downloaded = this.getItem("downloaded")
                if (downloaded) {
                    if (typeof video === "object") {
                        return downloaded.filter(item => item._id === video._id)
                    }
                    return downloaded.filter(item => item._id === video)

                }
                return false;
            },
            getItem(item){
                if (!localStorage.getItem(item)) {
                    return;
                }
                return JSON.parse(localStorage.getItem(item))
            },
            setItem(item, value){
                localStorage.setItem(item, JSON.stringify(value))
            },

            nextOrder(order, video_youtube, noFree, course){


                if (noFree) {
                    this.showMessage = true
                    return
                }

                this.gettingNextVideo(false)

                if (this.isDownloadedExist(course).length) {
                    this.videoPlay = this.isDownloadedExist(course)[0]
                    ipcRenderer.send('nextDownload', this.videoPlay)
                } else {
                    this.videoPlay = this.courseDetail.list.filter(item => item.video_youtube === video_youtube)[0]
                    ipcRenderer.send('nextVideo', video_youtube)
                }


                this.addLastWatch(this.videoPlay)

                this.lessonView(this.videoPlay._id).then(response => {
                    this.documents = response
                })

                this.order = order
            },
            previousVideo(){

                if (this.order <= 1) {
                    return
                }

                this.order = this.order - 1

                let videoOrder = this.courseDetail.list.map(item => item.order)
                let lastOrder = Math.max(...videoOrder)
                let firstOrder = Math.min(...videoOrder)
                let previousVideo = this.order

                let videoId;

                if (this.isDownloadedExist(this.order).length) {
                    this.videoPlay = this.isDownloadedExist(this.order)[0]
                    videoId = this.videoPlay._id
                    this.order = previousVideo
                    this.gettingNextVideo(false)
                    ipcRenderer.send('nextDownload', this.videoPlay)
                } else {
                    let videoFilter = this.courseDetail.list.filter(item => item.order === previousVideo)
                    this.videoPlay = videoFilter[0]
                    let isFree = videoFilter.map(item => item.free_watch)[0]
                    videoId = videoFilter.map(item => item._id)[0]
                    if (isFree === 0 && this.courseDetail.is_buy === 0) {
                        return
                    }

                    let video_youtube = this.courseDetail.list.filter(item => item.order === previousVideo).map(ytId => ytId.video_youtube)
                    this.order = previousVideo
                    this.gettingNextVideo(false)
                    ipcRenderer.send('nextVideo', video_youtube)

                }
                this.addLastWatch(this.videoPlay);

                if (previousVideo >= lastOrder) {
                    previousVideo = firstOrder
                    this.order = firstOrder
                    this.$nextTick(() => {
                        this.$refs.courseDetail.scrollTo(0, 0)
                    });
                }

                this.lessonView(videoId).then(response => {
                    this.documents = response
                })
            },
            onPlayerEnded(){
                let videoOrder = this.courseDetail.list.map(item => item.order)
                let lastOrder = Math.max(...videoOrder)
                if (this.order === lastOrder) {
                    this.$nextTick(() => {
                        this.$refs.courseDetail.scrollTop = 0;
                    });
                    this.order = 1
                    return
                }

                let nextVideo = this.order + 1

                let videoFilter = this.courseDetail.list.filter(item => item.order === nextVideo)
                let isFree = videoFilter.map(item => item.free_watch)[0]
                let videoId = videoFilter.map(item => item._id)[0]

                if (isFree === 0 && this.courseDetail.is_buy === 0) {
                    return
                }

                this.videoPlay = videoFilter[0]
                this.addLastWatch(this.videoPlay)


                let video_youtube = this.courseDetail.list.filter(item => item.order == nextVideo).map(ytId => ytId.video_youtube)

                this.order = nextVideo
                this.gettingNextVideo(false)
                ipcRenderer.send('nextVideo', video_youtube)

                this.lessonView(videoId).then(response => {
                    this.documents = response
                })
            },
            cutString(text, limit){
                if (!text) {
                    return
                }
                return helper.cutString(text, limit)
            },
            getPathFromUrl(url) {
                return url.split("?")[0];
            },
            kFormatter(num){
                return helper.kFormatter(num)
            },

            closeMessage(){
                this.showMessage = false
            },

            closePaymentMethod(){
                this.showCartForm = false
            },
            showCart(){
                this.addCart(this.courseDetail.course._id)

                this.showMessage = false
                this.showCartForm = true
            },

            downloadSucceed(video = null){
                let downloaded = [];
                if (localStorage.getItem("downloaded")) {
                    downloaded = JSON.parse(localStorage.getItem("downloaded"))
                }

                if (video) {
                    downloaded.push(video)
                }

                downloaded = downloaded.filter((value, index, self) => self.findIndex((m) => m._id === value._id) === index)


                localStorage.setItem("downloaded", JSON.stringify(downloaded))

                this.downloaded = downloaded
            },

            closeCart(){
                this.showCartForm = false
            },
            addLastWatch(videoPlay){
                if (videoPlay.last_watch) {
                    this.last_watch = videoPlay.last_watch.mark

                    if (videoPlay.last_watch.percentage >= 100) {
                        this.last_watch = 0
                    }
                } else {
                    this.last_watch = 0
                }
            },

            buy(){
                this.addCart(this.courseDetail.course._id).then(() => {
                    this.showCartForm = true
                })
            },
            lastWatchMark(percentage){
                return `background: linear-gradient(90deg, rgb(255, 14, 9) ${percentage}%, rgb(214, 214, 214) 0%)`;
            },

            scrollToBottom()
            {
                setTimeout(() => {
                    let vdActive = document.getElementById("vdActive");
                    if (this.order > 5) {
                        this.$refs.feed.scrollTop = vdActive.getBoundingClientRect().top - 120
                    }
                }, 3000)
            },
        },
        mounted(){

            if (this.courseDetail.list && this.courseDetail.list.lenght > 1) {
                this.lastOrder = (this.courseDetail.list[this.courseDetail.list.length - 1].order)
            }
            this.scrollToBottom()
        },
        created(){

            localStorage.removeItem("downloadingStorage")

            window.addEventListener('resize', this.handleResize);

            this.downloadSucceed()

            this.handleResize();
            this.order = this.$route.params.order ? this.$route.params.order : 1
            this.loadingPlay = true

            ipcRenderer.on("downloadFailed", (event, arg) => {
                this.downloadingVideo = this.downloadingVideo.filter(item => item !== arg._id)
                err.err("កំរិតវីដេអូនេះមិនអាចទាញយកបានទេ សូមធ្វើការជ្រើសរើសម្ដងទៀត", 2)
            })

            ipcRenderer.on("downloadComplete", (event, arg) => {
                let downloadingStorage = localStorage.getItem("downloadingStorage")
                if (downloadingStorage) {
                    downloadingStorage = JSON.parse(localStorage.getItem("downloadingStorage"))

                    const index = downloadingStorage.indexOf(arg._id);
                    if (index > -1) {
                        downloadingStorage.splice(index, 1);
                    }

                    localStorage.setItem("downloadingStorage", JSON.stringify(downloadingStorage))
                }
                this.downloadSucceed(arg)
                this.getDownloading()
            }),
                this.getCourseDetail({
                    courseId: this.$route.params.courseId,
                    order: this.order,
                    type: "1"
                }).then(() => {
                    this.courseDetail.list.filter(item => {
                        if (item.order === this.order) {
                            if (this.isDownloadedExist(item).length) {
                                this.videoPlay = this.isDownloadedExist(item)[0]
                                this.addLastWatch(this.videoPlay)
                                ipcRenderer.send('nextDownload', this.videoPlay)
                            } else {
                                ipcRenderer.send('youtubeVideo', item.video_youtube)
                                this.videoPlay = item
                                this.addLastWatch(this.videoPlay)
                                this.lessonView(this.videoPlay._id).then(response => {
                                    this.documents = response
                                })
                            }
                        }
                    })
                })
            this.loadingPlay = false
        }
    }
</script>
<style>
    svg.svg-inline--fa.fa-caret-right.fa-w-6 {
        width: 10px;
        color: #606060;
    }

    body {
        overflow: hidden;
    }

    ::-webkit-scrollbar {
        width: 0px;
    }

    #courseList::-webkit-scrollbar {
        width: 5px;
    }

    .percentage {
        -webkit-appearance: none;
        width: 100%;
        height: 3px;
        background: linear-gradient(90deg, rgb(255, 14, 9) 0%, rgb(255, 255, 255) 0%);
        outline: none;
        box-shadow: 0px 1px 10px black;
        cursor: pointer;
    }

    .percentage {
        height: 3px;
        background: #fffffff;
        box-shadow: none;
    }

    .percentage::-webkit-slider-thumb {
        -webkit-appearance: none;
        appearance: none;
        background: #ff0e09;
        cursor: pointer;
    }

    #feed::-webkit-scrollbar {
        width: 5px !important;
    }

    #feed::-webkit-scrollbar-track {
        -webkit-box-shadow: inset 0 0 0px rgba(0, 0, 0, 0.3) !important;
    }

    #feed::-webkit-scrollbar-thumb {
        -webkit-box-shadow: inset 0 0 0px rgba(0, 0, 0, 0.5) !important;
    }

</style>
