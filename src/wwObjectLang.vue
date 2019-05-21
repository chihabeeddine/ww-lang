<template>
    <div class="ww-lang" :style="{'color': mainColor}">
        <div class="ww-lang-flag-title">
            <img :src="flag">
            <span :style="{'color': iconColor}" class="fas fa-chevron-down"></span>
        </div>

        <div class="hover-zone">
            <div class="triangle"></div>
            <div class="lang-container" :style="{'color': mainColor}">
                <div class="lang" :src="flag" v-for="lang in availableLangs" :key="lang" @click="setLang(lang)">
                    <div class="ww-lang-flag selected-lang">
                        <img :src="displayFlag(lang)">
                        {{displayLang(lang)}}
                    </div>
                </div>
            </div>
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
                    fr: 'French'
                },
                fr: {
                    en: 'Anglais',
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
        },
        mainColor() {
            return this.wwObject.content.data.mainColor || '#2c2c2c'
        },
        iconColor() {
            return this.wwObject.content.data.iconColor || '#d9d9d9'
        },
        flag() {
            return wwLib.wwApiRequests._getCdnUrl() + 'public/images/flags/' + this.currentLang + '.png'
        },
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
        },
        displayFlag(lang) {
            return wwLib.wwApiRequests._getCdnUrl() + 'public/images/flags/' + lang + '.png'
        },

        async edit() {
            wwLib.wwObjectHover.setLock(this);
            let editList = {
                WWLANG_COLOR: {
                    title: {
                        en: 'Edit the color',
                        fr: 'Configurer la couleur'
                    },
                    desc: {
                        en: 'Edit the object text color',
                        fr: 'Éditer la couleur du texte'
                    },
                    icon: 'wwi wwi-color',
                    shortcut: 'c',
                    next: 'WWLANG_COLOR'
                }
            }
            /* todo open a popupform to change more colors */
            wwLib.wwPopups.addStory('WWLANG_COLOR', {
                title: {
                    en: 'Edit the object text color',
                    fr: 'Éditer la couleur du texte'
                },
                type: 'wwPopupColorPicker',
                buttons: {
                    NEXT: {
                        text: {
                            en: 'Ok',
                            fr: 'Ok'
                        },
                        next: false
                    }
                }
            })

            wwLib.wwPopups.addStory('WWLANG_EDIT', {
                title: {
                    en: 'Edit lang menu',
                    fr: 'Editer le menu langue '
                },
                type: 'wwPopupEditWwObject',
                buttons: null,
                storyData: {
                    list: editList
                }
            })

            let options = {
                firstPage: 'WWLANG_EDIT',
                data: {
                    wwObject: this.wwObject
                }
            }

            try {
                const result = await wwLib.wwPopups.open(options);
                console.log('RESULT : ', result)

                /*=============================================m_ÔÔ_m=============================================\
                  STYLE
                \================================================================================================*/
                if (typeof (result.color) != 'undefined') {
                    this.wwObject.content.data.mainColor = result.color;
                }

                this.wwObjectCtrl.update(this.wwObject);
                this.wwObjectCtrl.globalEdit(result);
                console.log(this.wwObject.content.data)
            } catch (error) {
                console.log(error);
            }
            wwLib.wwObjectHover.removeLock();
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
    min-width: 50px;
    height: 100%;
    display: flex;
    align-items: center;
    cursor: pointer;
    .ww-lang-flag-title {
        position: relative;
        cursor: pointer;
        display: flex;
        align-items: center;
        font-size: 1.2rem;
        img {
            margin-left: 5px;
            margin-right: 5px;
            width: 30px;
            border-radius: 10px;
        }
    }
    .hover-zone {
        position: absolute;
        bottom: 0px;
        transform: translate(-70%, 100%);
        visibility: hidden;
        opacity: 0;
        transition: visibility 0s, opacity 0.2s linear;
        z-index: 10;
        .lang-container {
            display: flex;
            flex-direction: column;
            align-items: center;
            width: 200px;
            background-color: white;
            box-shadow: 0px 0px 3px 0px #bfbfbf;
            border-radius: 10px;
            font-family: sans-serif;
            padding: 5px 0;
            color: black;
            box-shadow: black;
            .lang {
                width: 100%;
                padding: 5px 10px;
                border-radius: 10px;
                cursor: pointer;
                &:hover {
                    background-color: #fafafa;
                }
                .ww-lang-flag {
                    position: relative;
                    cursor: pointer;
                    display: flex;
                    align-items: center;
                    font-size: 1.2rem;
                    img {
                        margin-left: 5px;
                        margin-right: 5px;
                        width: 20px;
                        border-radius: 7px;
                    }
                    &:hover {
                        color: #8f1afe;
                    }
                }
            }
        }
    }
    &:hover {
        .hover-zone {
            visibility: visible;
            opacity: 1;
        }
    }
    .triangle {
        width: 20px;
        height: 20px;
        position: relative;
        overflow: hidden;
        float: right;
        right: 30px;
        top: 2px;
        box-shadow: 0 16px 10px -17px rgba(0, 0, 0, 0.5);
    }
    .triangle:after {
        content: "";
        position: absolute;
        width: 15px;
        height: 15px;
        background: #ffffff;
        transform: rotate(45deg);
        top: 11px;
        left: 2px;
        box-shadow: 0px 0px 6px -2px rgba(0, 0, 0, 0.5);
    }
}
</style>
