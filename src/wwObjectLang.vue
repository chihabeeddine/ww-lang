<template>
    <div class="ww-lang" :style="{'color': mainColor}">
        <div class="current-lang">
            {{displayLang(currentLang)}}
            <div class="triangle-container">
                <span class="triangle" :style="{'border-top-color': mainColor}"></span>
            </div>
        </div>
        <div class="hover-zone">
            <div class="lang-container" :style="{'border-color': mainColor}">
                <div class="lang" v-for="lang in availableLangs" :key="lang" @click="setLang(lang)">{{displayLang(lang)}}</div>
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
                    en: 'en',
                    fr: 'en'
                },
                fr: {
                    en: 'fr',
                    fr: 'fr'
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
        transform: translateY(100%);
        visibility: hidden;
        opacity: 0;
        transition: visibility 0s, opacity 0.2s linear;
        z-index: 10;
        left: -10px;
        .lang-container {
            display: flex;
            flex-direction: column;
            align-items: center;
            width: 70px;
            background-color: white;
            border: 1px solid #979797;
            border-radius: 3px;
            margin-top: 10px;

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
    .current-lang {
        position: relative;
        white-space: nowrap;
        .triangle-container {
            position: relative;
            width: 9px;
            height: 10px;
            display: inline-block;
            margin-left: 5px;
            .triangle {
                position: absolute;
                top: 55%;
                transform: translateY(-50%);
                width: 0;
                height: 0;
                border-style: solid;
                border-width: 7px 4.5px 0 4.5px;
                border-color: #2c2c2c transparent transparent transparent;
            }
        }
    }
}
</style>
