<template>

    <div class="dropdown"
        v-click-outside="hide"
        :class="{ 'is-active': show }">
        <div class="dropdown-trigger"
            @click="show=!show">
            <button class="button"
                aria-haspopup="true"
                aria-controls="dropdown-menu">
                <slot name="label"></slot>
                <span class="icon is-small angle"
                        :aria-hidden="!show">
                    <fa icon="angle-down"></fa>
                </span>
            </button>
        </div>
        <div class="dropdown-menu animated"
            role="menu"
            :class="{ 'fadeIn': show, 'fadeOut': !show }"
            v-if="show"
            :style="widthStyle">
            <div class="dropdown-content has-text-centered"
                :style="[widthStyle, heightStyle]">
                <slot></slot>
            </div>
        </div>
    </div>

</template>

<script>

import vClickOutside from 'v-click-outside';
import fontawesome from '@fortawesome/fontawesome';
import { faAngleDown } from '@fortawesome/fontawesome-free-solid/shakable.es';

fontawesome.library.add(faAngleDown);

export default {
    name: 'Dropdown',

    directives: {
        clickOutside: vClickOutside.directive,
    },

    props: {
        width: {
            type: Number,
            default: 64,
        },
        height: {
            type: Number,
            default: 200,
        },
    },

    computed: {
        widthStyle() {
            return {
                'min-width': `${this.width}px`,
            };
        },
        heightStyle() {
            return {
                'max-height': `${this.height}px`,
            };
        },
    },

    data() {
        return {
            show: false,
        };
    },

    methods: {
        hide() {
            this.show = false;
        },
    },
};

</script>

<style lang="scss" scoped>

    .dropdown-menu {
        min-width: unset;

        .dropdown-content {
            min-width: unset;
            overflow-y: auto;

            a.dropdown-item {
                padding: .375rem 1rem;
            }
        }
    }

    .icon.angle {
        transition: transform .300s ease;

        &[aria-hidden="true"] {
            transform: rotate(180deg);
        }
    }

</style>
