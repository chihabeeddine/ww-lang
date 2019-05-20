<template>
    <div class="ww-lang" :style="{'color': mainColor}">
        <div class="ww-lang-flag-title">
            <img :src="flag">
            <span class="wwi wwi-chevron-down"></span>
        </div>

        <div class="hover-zone">
            <div class="lang-container" :style="{'border-color': mainColor}">
                <div class="triangle-container">
                    <span class="triangle"></span>
                </div>
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
            console.log('erogheoprihg')
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
.ww-lang-flag-title {
    position: relative;
    //    position: absolute;
    //   right: 0px;
    cursor: pointer;
    display: flex;
    align-items: center;
    font-size: 1.2rem;
    img {
        margin-left: 5px;
        margin-right: 5px;
        width: 30px;
    }
}
.ww-lang-flag {
    position: relative;
    //    position: absolute;
    //   right: 0px;
    cursor: pointer;
    display: flex;
    align-items: center;
    font-size: 1.2rem;
    img {
        margin-left: 5px;
        margin-right: 5px;
        width: 20px;
    }

    &:hover {
        color: #8f1afe;
    }
}

.ww-lang {
    position: relative;
    min-width: 50px;
    height: 100%;
    display: flex;
    align-items: center;
    cursor: pointer;
    .hover-zone {
        position: absolute;
        bottom: 0px;
        //transform: translateY(100%);
        transform: translate(-70%, 100%);
        visibility: hidden;
        opacity: 0;
        transition: visibility 0s, opacity 0.2s linear;
        z-index: 10;
        //left: -10px;
        .lang-container {
            display: flex;
            flex-direction: column;
            align-items: center;
            width: 200px;
            background-color: white;
            box-shadow: 0px 0px 3px 0px #bfbfbf;
            border-radius: 10px;
            //border: 1px solid #979797;
            //border-radius: 3px;
            //margin-top: 10px;
            padding: 5px 0;
            color: black;
            box-shadow: black;

            .lang {
                width: 100%;
                padding: 5px 10px;
                cursor: pointer;
                &:hover {
                    background-color: #fafafa;
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

    .triangle-container {
        position: relative;
        width: 0;
        height: 0;

        .triangle {
            position: absolute;
            //top: 55%;
            //transform: translateY(-50%);
            transform: translate(125%, -135%);
            //box-shadow: 0px 0px 3px 0px #bfbfbf;
            width: 0;
            height: 0;
            border-left: 15px solid transparent;
            border-right: 15px solid transparent;
            border-bottom: 15px solid #ffffff;
        }
    }
}
</style>
