<template>
    <div class="form-date-range">
        <bk-date-range class="bk-date-range"
            :placeholder="placeholder"
            :disabled="disabled"
            :position="position"
            :range-separator="rangeSeparator"
            :quick-select="quickSelect"
            :ranges="ranges"
            :timer="timer"
            :start-date="startDate"
            :end-date="endDate"
            @change="handleChange">
        </bk-date-range>
        <div class="icon-box" :class="{ 'icon-toggle': hoverToggle }">
            <i class="bk-icon icon-close-circle-shape"
                @click="handleClearData"
                v-if="showClose">
            </i>
        </div>
    </div>
</template>

<script>
    export default {
        name: 'cmdb-form-date-range',
        props: {
            value: {
                type: Array,
                default () {
                    return []
                }
            },
            placeholder: {
                type: String,
                default: ''
            },
            disabled: {
                type: Boolean,
                default: false
            },
            position: {
                type: String,
                default: 'bottom-right',
                validator (val) {
                    return ['top', 'bottom', 'left', 'right', 'top-left', 'top-right', 'bottom-left', 'bottom-right'].includes(val)
                }
            },
            rangeSeparator: {
                type: String,
                default: ' - '
            },
            quickSelect: {
                type: Boolean,
                default: false
            },
            ranges: {
                type: Object
            },
            timer: {
                type: Boolean,
                default: false
            },
            showClose: {
                type: Boolean,
                default: false
            }
        },
        data () {
            return {
                localSelected: [],
                hoverToggle: false
            }
        },
        computed: {
            startDate () {
                return this.localSelected.length ? this.localSelected[0] : ''
            },
            endDate () {
                return this.localSelected.length ? this.localSelected[1] : ''
            },
            separator () {
                return ` ${this.rangeSeparator} `
            }
        },
        watch: {
            value (value) {
                this.setLocalSelected()
            },
            localSelected (localSelected, oldSelected) {
                const value = Array.isArray(this.value) ? this.value : []
                if (localSelected.join(this.separator) !== value.join(this.separator)) {
                    this.$emit('input', [...localSelected])
                    this.$emit('on-change', [...localSelected], [...oldSelected])
                }
            }
        },
        created () {
            this.setLocalSelected()
        },
        methods: {
            setLocalSelected () {
                const value = Array.isArray(this.value) ? this.value : []
                if (this.localSelected.join(this.separator) !== value.join(this.separator)) {
                    this.localSelected = [...value]
                }
            },
            handleChange (oldValue, newValue) {
                this.hoverToggle = true
                this.localSelected = newValue.split(this.separator)
            },
            handleClearData () {
                this.localSelected = []
                const setTimer = setTimeout(() => {
                    this.hoverToggle = false
                    clearTimeout(setTimer)
                })
            }
        }
    }
</script>

<style lang="scss" scoped>
    .form-date-range{
        position: relative;
        display: inline-block;
        vertical-align: middle;
        &:hover .icon-toggle {
            display: block;
        }
    }
    .bk-date-range{
        width: 100%;
        white-space: normal;
    }
    .icon-box {
        position: absolute;
        background-color: #ffffff;
        top: 1px;
        right: 1px;
        padding: 7px;
        display: none;
        .icon-close-circle-shape {
            font-size: 20px;
            cursor: pointer;
        }
    }
</style>
