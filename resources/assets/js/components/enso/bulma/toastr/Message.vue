<template>

    <transition appear
        :enter-active-class="enterClass"
        :leave-active-class="leaveClass"
        @after-leave="destroy">
        <article :class="['message toastr animated', type ? `is-${type}` : '']"
            v-if="show">
            <div class="message-header">
                <span class="icon is-small" v-if="icon">
                    <fa :icon="icons[type]"></fa>
                </span>
                <span class="is-pulled-right">
                    {{ i18n(displayTitle) }}
                </span>
                <button class="delete"
                    @click="close()"
                    v-if="closeButton">
                </button>
            </div>
            <div class="message-body"
                v-if="message">
                {{ i18n(message) }}
            </div>
        </article>
    </transition>

</template>

<script>

import Vue from 'vue';

import fontawesome from '@fortawesome/fontawesome';
import {
    faArrowCircleRight, faInfoCircle, faCheckCircle, faExclamationCircle, faTimesCircle,
} from '@fortawesome/fontawesome-free-solid/shakable.es';

fontawesome.library.add([
    faArrowCircleRight, faInfoCircle, faCheckCircle, faExclamationCircle, faTimesCircle,
]);

const types = ['default', 'primary', 'info', 'success', 'warning', 'danger'];
const positions = ['left', 'right', 'center'];
const icons = {
    default: null,
    primary: 'arrow-circle-right',
    info: 'info-circle',
    success: 'check-circle',
    warning: 'exclamation-circle',
    danger: 'times-circle',
};
const titles = {
    default: 'Message',
    primary: 'Message',
    info: 'Info',
    success: 'Success',
    warning: 'Warning',
    danger: 'Error',
};

export default {
    name: 'Message',

    props: {
        type: {
            type: String,
            required: true,
            validator: val => types.includes(val),
        },
        title: {
            type: String,
            default: null,
        },
        message: {
            type: String,
            required: true,
        },
        position: {
            type: String,
            default: 'right',
            validator: val => positions.includes(val),
        },
        duration: {
            type: Number,
            default: 3500,
            validator: val => val > 0,
        },
        closeButton: {
            type: Boolean,
            default: true,
        },
        container: {
            type: String,
            default: 'toastr-wrapper',
        },
        i18n: {
            type: Function,
            default: val => val,
        },
    },

    data() {
        return {
            wrapper: null,
            icons,
            show: true,
        };
    },

    computed: {
        direction() {
            if (this.position === 'center') return 'Down';
            if (this.position === 'right') return 'Right';
            return 'Left';
        },
        enterClass() {
            return `bounceIn${this.direction}`;
        },

        leaveClass() {
            return this.position === 'center'
                ? 'bounceOutUp'
                : `bounceOut${this.direction}`;
        },

        icon() {
            return this.icons[this.type];
        },
        containerClass() {
            return `${this.container} ${this.position}`;
        },
        containerSelector() {
            return `.${this.container}.${this.position}`;
        },
        displayTitle() {
            return this.title || titles[this.type];
        },
    },

    created() {
        const wrapper = document.querySelector(this.containerSelector);

        if (!wrapper) {
            const { containerClass } = this;

            const ToastrWrapper = Vue.extend({
                name: 'ToastrWrapper',
                render(h) {
                    return h('div', {
                        class: `${containerClass}`,
                    });
                },
            });

            this.wrapper = new ToastrWrapper().$mount();
            document.body.appendChild(this.wrapper.$el);
        } else {
            this.wrapper = wrapper.__vue__;
        }
    },

    mounted() {
        this.wrapper.$el.appendChild(this.$el);
        delete this.wrapper;
        this.timer = setTimeout(() => this.close(), this.duration);
    },

    methods: {
        close() {
            clearTimeout(this.timer);
            this.show = false;
        },

        destroy() {
            this.$destroy();
        },
    },
};

</script>

<style lang="scss">

    .toastr-wrapper {
        position: fixed;
        display: flex;
        flex-direction: column;
        z-index: 9999;
        pointer-events: none;
        top: 2em;

        &.left {
            margin-right: auto;
            left: 2em;
        }

        &.right {
            margin-left: auto;
            right: 2em;
        }

        &.center {
            margin-left: calc(50% - 150px);
        }

        .toastr.message {
            width: 300px;
            margin-bottom: 6px;
            pointer-events: auto;
            position: relative;
            z-index: 9999;
            position: relative;
            -webkit-box-shadow: 0px 0px 5px 1px rgba(133,133,133,1);
            -moz-box-shadow: 0px 0px 5px 1px rgba(133,133,133,1);
            box-shadow: 0px 0px 5px 1px rgba(133,133,133,1);

            .message-header {

                .icon {
                    margin-right: 8px;
                }
            }
        }
    }

</style>
