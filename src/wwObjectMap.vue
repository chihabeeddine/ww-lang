<template>
    <div class="ww-lang">
        <div class="current-lang">
            {{displayLang(currentLang)}}
            <span class="triangle"></span>
        </div>
        <div class="lang-container">
            <div class="lang" v-for="lang in availableLangs" :key="lang" @click="setLang(lang)">{{displayLang(lang)}}</div>
        </div>
    </div>
</template>
 

<script>			
import Vue from 'vue';

export default {
    name: "__COMPONENT_NAME__",
    props: {
        wwObjectCtrl: Object,
        wwAttrs: {
            type: Object,
            default: {}
        }
    },
    data() {
        return {
            currentLang: wwLib.wwLang.lang,
            availableLangs: wwLib.wwLang.availableLangs,
            languages: {
                en: {
                    en: 'English',
                    fr: 'Anglais'
                },
                fr: {
                    en: 'French',
                    fr: 'Français'
                }
            }
        }
    },
    computed: {
        wwObject() {
            return this.wwObjectCtrl.get();
        },
        editMode() {
            return this.wwObjectCtrl.getEditMode() == 'CONTENT'
        }
    },
    watch: {
    },
    methods: {
        init() {
            this.loaded = true
        },
        setLang(lang) {
            wwLib.wwLang.setLang(lang)
            this.currentLang = lang
        },
        displayLang(lang) {
            return this.languages[lang][lang]
        }
    },
    mounted() {
        this.init();

        wwLib.wwElementsStyle.applyAllStyles({
            wwObject: this.wwObject,
            lastWwObject: null,
            element: this.$el,
            noAnim: this.wwAttrs.wwNoAnim,
            noClass: false,
        });

        this.$emit('ww-loaded', this);

    },
    beforeDestroyed() {
        //window.removeEventListener('resize', this.wwOnResize);
    }
};
</script>

<style lang="scss" scoped>
.ww-lang {
    position: relative;
    width: 150px;
    .lang-container {
        display: flex;
        flex-direction: column;
        width: 150px;
        background-color: white;
        border: 1px solid #979797;
        border-radius: 3px;
        margin-top: 5px;
        visibility: hidden;
        opacity: 0;
        transition: visibility 0s, opacity 0.8s linear;
        .lang {
            padding: 5px 10px;
            cursor: pointer;
            &:hover {
                background-color: #fafafa;
            }
        }
    }
    &:hover {
        .lang-container {
            visibility: visible;
            opacity: 1;
        }
    }
    .current-lang {
        position: relative;
        .triangle {
            position: absolute;
            bottom: 0;
            transform: translateY(-100%);
            margin-left: 5px;
            width: 0;
            height: 0;
            border-style: solid;
            border-width: 8px 5px 0 5px;
            border-color: #979797 transparent transparent transparent;
        }
    }
}
</style>
