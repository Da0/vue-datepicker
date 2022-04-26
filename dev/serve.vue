<template>
    <div class="wrapper">
        <Datepicker
            v-model="selectedDate"
            placeholder="Select Date"
            :range="true"
            :inline="true"
            :no-swipe="true"
            :auto-apply="true"
            locale="ru-RU"
            :month-change-on-scroll="false"
            :month-change-on-arrows="false"
            :enable-time-picker="false"
            :partial-range="false"
            :multi-calendars="12"
            :multi-calendars-solo="true"
            month-name-format="long"
            :month-year-component="MonthYear"
            :keep-action-row="false"
            :hide-offset-dates="false"
            :prevent-min-max-navigation="false"
            v-bind="{ minDate }"
        />
    </div>
</template>

<script lang="ts" setup>
    import { ref, defineAsyncComponent, computed } from 'vue';
    import Datepicker from '../src/VueDatePicker/VueDatePicker.vue';
    const MonthYear = computed(() => defineAsyncComponent(() => import('./MonthYearCustom.vue')));
    const minDate = ref(new Date());

    const selectedDate = ref([new Date('03-27-2022'), new Date('03-30-2022')]);
</script>

<style lang="scss">
    @import 'src/VueDatePicker/style/main';
    body {
        margin: 0;
    }

    .wrapper {
        padding: 20px;
    }

    .dp__main {
        max-width: 330px;
        max-height: calc(100vh - 40px);
        overflow: auto;
        border: none;
        margin: 0 auto;
        display: block;

        .dp__menu {
            border: none;
            border-radius: 0;
            padding-bottom: 20px;
        }

        .dp__instance_calendar > .dp__flex_display {
            flex-direction: column;
        }

        .dp__calendar_header_separator {
            display: none;
        }

        .dp__calendar_header_item {
            display: flex;
            align-items: center;
            justify-content: center;
            font-weight: 600;
            text-transform: capitalize;
        }

        .dp__today {
            border-radius: 50%;
            color: var(--dp-primary-color);
            font-weight: 600;
            border-color: transparent;
        }

        .dp__pointer:hover {
            border: 1px solid var(--dp-primary-color);
        }

        .dp__calendar_item {
            position: relative;
            z-index: 1;
        }

        .dp__range_start:not(.dp__range_end),
        .dp__range_end:not(.dp__range_start),
        .dp__cell_auto_range_start:not(.dp__cell_auto_range_end),
        .dp__date_hover_start:hover,
        .dp__cell_auto_range_end:not(.dp__cell_auto_range_start),
        .dp__date_hover_end:not(.dp__date_hover_start):hover {
            &:not(.dp__cell_disabled):not(.dp__cell_offset) {
                background-color: var(--dp-primary-color);
                color: var(--dp-primary-text-color);
                &::after {
                    content: '';
                    position: absolute;
                    top: -1px;
                    bottom: -1px;
                    width: 50%;
                    background-color: var(--dp-hover-color);
                    z-index: -1;
                }
            }
        }
        .dp__range_start,
        .dp__cell_auto_range_start,
        .dp__date_hover_start:hover {
            border-bottom-right-radius: 50%;
            border-top-right-radius: 50%;
            &::after {
                right: -1px;
            }
        }
        .dp__range_end,
        .dp__cell_auto_range_end,
        .dp__date_hover_end:hover {
            border-bottom-left-radius: 50%;
            border-top-left-radius: 50%;
            &::after {
                left: -1px;
            }
        }

        .dp__range_between {
            &,
            &:hover {
                border: none;
            }
            &:hover {
                position: relative;
                &::after {
                    content: '';
                    position: absolute;
                    left: 0;
                    top: 0;
                    width: 100%;
                    height: 100%;
                    border-radius: 50%;
                    border: 1px solid var(--dp-primary-color);
                }
            }
        }

        .dp__calendar_next {
            margin-left: 0;
        }

        .dp__cell_disabled {
            &,
            &:hover,
            &.dp__range_start,
            &.dp__range_end {
                color: var(--dp-secondary-color);
                background: none;
            }
        }

        .dp__cell_offset {
            cursor: not-allowed;
            color: var(--dp-secondary-color);
            &:hover {
                border-color: transparent;
            }
        }
    }
</style>
