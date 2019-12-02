<!-- This is a Vue.js single file component. -->
<!-- Check the Vue.js doc here :  -->
<!-- https://vuejs.org/v2/guide/ -->

<!-- This is your HTML -->
<template>
    <div class="two-column-slash" :style="customStyle">
        <!-- wwManager:start -->
        <wwSectionEditMenu v-bind:sectionCtrl="sectionCtrl"></wwSectionEditMenu>
        <!-- wwManager:end -->

        <!-- This is the background of the section -->
        <wwObject class="background" v-bind:ww-object="section.data.background" ww-category="background"></wwObject>

        <div class="content">
            <!-- left column -->
            <div class="left-column">
                <wwLayoutColumn tag="div" ww-default="ww-text" :ww-list="section.data.leftColumn" class="list" @ww-add="add(section.data.leftColumn, $event)" @ww-remove="remove(section.data.leftColumn, $event)">
                    <wwObject tag="div" v-for="object in section.data.leftColumn" :key="object.uniqueId" :ww-object="object"></wwObject>
                </wwLayoutColumn>
            </div>

            <!-- slash column -->
            <div ref="slashContainer" class="slash-container">
                <div class="left"></div>
                <div class="right"></div>
            </div>

            <!-- right column -->
            <div class="right-column">
                <wwLayoutColumn tag="div" ww-default="ww-text" :ww-list="section.data.rightColumn" class="list" @ww-add="add(section.data.rightColumn, $event)" @ww-remove="remove(section.data.rightColumn, $event)">
                    <wwObject tag="div" v-for="object in section.data.rightColumn" :key="object.uniqueId" :ww-object="object"></wwObject>
                </wwLayoutColumn>
            </div>
        </div>
    </div>
</template>

<!-- This is your Javascript -->
<!-- ✨ Here comes the magic ✨ -->
<script>
import ResizeObserver from 'resize-observer-polyfill';
export default {
    name: "__COMPONENT_NAME__",
    props: {
        sectionCtrl: Object
    },
    data() {
        return {
            sectionHeight: 0
        }
    },
    computed: {
        section() {
            return this.sectionCtrl.get();
        },
        customStyle() {
            const shadowColorAfter = this.section.data.animAtHover ? this.section.data.shadowColorAfter : this.section.data.shadowColor;
            const pixelsToScrollTop = `-${this.section.data.animAtHover ? this.section.data.pixelsToScrollTop : '0'}px`;
            return {
                '--sectionHeight': `${this.sectionHeight}px`,
            }
        }

    },
    created() {
        this.init()
    },
    mounted() {
        this.sectionHeight = this.$refs.slashContainer.clientHeight;
        const myObserver = new ResizeObserver(entries => {
            for (let entry of entries) {
                this.updateVars()
            }

        });
        myObserver.observe(this.$refs.slashContainer);
    },
    methods: {
        init() {
            // Initialize section data
            let needUpdate = false;

            this.section.data = this.section.data || {};

            if (!this.section.data.background) {
                this.section.data.background = wwLib.wwObject.getDefault({
                    type: 'ww-color'
                });
                needUpdate = true
            }

            if (!this.section.data.leftSlash) {
                this.section.data.leftSlash = wwLib.wwObject.getDefault({
                    type: 'ww-color'
                });
                needUpdate = true
            }

            if (!this.section.data.rightSlash) {
                this.section.data.rightSlash = wwLib.wwObject.getDefault({
                    type: 'ww-color'
                });
                needUpdate = true
            }

            if (!this.section.data.leftColumn) {
                this.section.data.leftColumn = [];
                needUpdate = true;
            }

            if (!this.section.data.rightColumn) {
                this.section.data.rightColumn = [];
                needUpdate = true;
            }


            if (needUpdate) {
                this.sectionCtrl.update(this.section);
            }
        },
        updateVars() {
            if (this.$refs.slashContainer) {
                this.sectionHeight = this.$refs.slashContainer.clientHeight;
            }
        },

        // --------- EDITOR FUNCTIONS ---------
        /* wwManager:start */
        add(list, options) {
            try {
                list.splice(options.index, 0, options.wwObject);
                this.sectionCtrl.update(this.section);
            } catch (error) {
                wwLib.wwLog.error('ERROR : ', error);
            }
        },
        remove(list, options) {
            try {
                list.splice(options.index, 1);
                this.sectionCtrl.update(this.section);
            } catch (error) {
                wwLib.wwLog.error('ERROR : ', error);
            }
        }
        /* wwManager:end */
    }

};
</script>

<!-- This is your CSS -->
<style lang="scss" scoped>
.two-column-slash {
    .content {
        position: relative;
        height: 100%;
        width: 100%;
        display: flex;
        .left-column {
            flex-basis: 50%;
        }
        .right-column {
            flex-basis: 50%;
        }
        .slash-container {
            position: absolute;
            height: 100%;
            left: 50%;
            transform: translateX(-50%);
            .left {
                position: absolute;
                top: 0;
                right: 0;
                width: 0;
                height: 0;
                transform: translateX(50%);

                border-bottom: var(--sectionHeight) solid transparent;
                border-left: 100px solid black;
            }
            .right {
                position: absolute;
                top: 0;
                left: 0;
                width: 0;
                height: 0;
                transform: translateX(-50%);

                border-top: var(--sectionHeight) solid transparent;
                border-right: 100px solid white;
            }
        }
    }
}

.background {
    position: absolute;
    top: 0;
    left: 0;
    height: 100%;
    width: 100%;
}
</style>
