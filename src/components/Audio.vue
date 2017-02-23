<template lang="html">
    <div>
        <div class="weixinAudio" @click="startPlay">
            <audio :src="source.src" preload ref="audio" @canplay="readyPlay" @error="errorPlay" @ended="ended"></audio>
            <!-- 音频主要部分 -->
            <span class="db audio_area">
            <span v-if="isError">
            该音频出现了问题，无法播放
            </span>
            <span class="audio_wrp db" :class="{playing:isPlaying}" v-if="!isError">
        <span class="audio_play_area">
        <i class="icon_audio_default"></i>
        <i class="icon_audio_playing"></i>
        </span>
            <!-- 总时长 -->
            <span class="audio_length tips_global">{{time}}</span>
            <!-- 题目 -->
            <span class="db audio_info_area">
                <strong class="db audio_title">{{source.name}}</strong>
            </span>
            <!-- 进度条 -->
            <span class="progress_bar" :style="{width:percentWidth}"></span>
            </span>
            </span>
        </div>
    </div>
</template>
<script>
export default {
    data() {
            return {
                // 正在播放
                isPlaying: false,
                // 时间
                time: null,
                // 音频
                Audio: {},
                // 进度条
                percentWidth: 0,
                // 是否出错
                isError: false
            }
        },
        props: ['source'],
        mounted() {
            this.Audio = this.$refs.audio
        },
        methods: {
            // 准备播放阶段
            readyPlay() {
                this.updateTotalTime()
            },
            // 点击播放
            startPlay() {
                this.isPlaying = !this.isPlaying
                this.isPlaying ? this.play() : this.pause()
            },
            // 错误的时候
            errorPlay() {
                this.isError = true
            },
            // 开始播放
            play() {
                this.isPlaying && this.Audio.play()
                    // 时间更新=>进度条不断更新
                this.Audio.addEventListener('timeupdate', () => {
                    this.progressBar()
                })
            },
            // 暂停播放
            pause() {
                !this.isPlaying && this.Audio.pause()
                    // 当播放完的时候removeClass=>图标恢复原来那时
            },
            ended() {
                this.Audio.pause()
                this.isPlaying = this.Audio.currentTime !== this.Audio.duration
            },
            //进度条
            progressBar() {
                let percent = (this.Audio.currentTime * 100 / this.Audio.duration) + '%';
                let percentage = percent === "NaN%" ? `0%` : percent
                this.percentWidth = percentage
            },
            // 更新总时长
            updateTotalTime() {
                let time = this.Audio.duration,
                    minutes = this.getAudioMinutes(time),
                    seconds = this.getAudioSeconds(time),
                    audioTime = minutes + ":" + seconds;
                this.time = audioTime
            },
            //获取时间秒
            getAudioSeconds(str) {
                let string = str % 60;
                string = this.addZero(Math.floor(string), 2);
                string = (string < 60) ? string : "00";
                return string;
            },
            //获取时间分
            getAudioMinutes: function(str) {
                let string = str / 60;
                string = this.addZero(Math.floor(string), 2);
                string = (string < 60) ? string : "00";
                return string;
            },
            //时间+0
            addZero(word, howManyZero) {
                var word = String(word);
                while (word.length < howManyZero) word = "0" + word;
                return word;
            },
        }
}
</script>
<style lang="scss" scoped>
.db {
    display: block;
}

.weixinAudio {
    border: 1px solid #ebebeb;
    background-color: #fcfcfc;
    overflow: hidden;
    cursor: pointer;
    .audio_area {
        display: inline-block;
        width: 100%;
        padding: 12px;
        position: relative;
        font-weight: 400;
        text-decoration: none;
        text-size-adjust: none;
        .pic_audio_default {
            display: none;
            width: 18px;
        }
        // 进度条
        .progress_bar {
            position: absolute;
            left: 0;
            bottom: 0;
            background-color: #0cbb08;
            height: 2px;
        }
    }
    // 总时长
    .audio_length {
        float: right;
        font-size: 14px;
        margin-top: 11px;
        margin-left: 1em;
        color: #8c8c8c;
    }
    // 音频标题
    .audio_title {
        font-weight: 400;
        font-size: 17px;
        margin-top: 10px;
        width: auto;
        overflow: hidden;
        text-overflow: ellipsis;
        white-space: nowrap;
        word-wrap: normal;
    }
}

@mixin audio_play($position) {
    background: transparent url(../../static/image/iconloop.png) no-repeat 0 0;
    background-size: 54px 25px;
    background-position: $position center;
    width: 18px;
    height: 25px;
    vertical-align: middle;
    display: inline-block;
}

.audio_play_area {
    float: left;
    margin: 9px 22px 10px 5px;
    font-size: 0;
    width: 18px;
    height: 25px;
    .icon_audio_default {
        @include audio_play(-36px);
    }
    .icon_audio_playing {
        @include audio_play(0);
        animation: audio_playing 1s infinite;
        display: none;
    }
}

.playing .audio_play_area .icon_audio_default {
    display: block;
}

.audio_info_area {
    overflow: hidden;
}

.playing .audio_play_area .icon_audio_default {
    display: none;
}

.playing .audio_play_area .icon_audio_playing {
    display: inline-block;
}

@keyframes audio_playing {
    30% {
        background-position: 0px center;
    }
    31% {
        background-position: -18px center;
    }
    61% {
        background-position: -18px center;
    }
    61.5% {
        background-position: -36px center;
    }
    100% {
        background-position: -36px center;
    }
}
</style>
