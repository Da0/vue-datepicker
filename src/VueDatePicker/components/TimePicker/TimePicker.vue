<template>
    <div>
        <div
            v-if="!timePicker"
            :class="toggleButtonClass"
            role="button"
            :aria-label="ariaLabels.openTimePicker"
            tabindex="0"
            @keydown.enter="toggleTimePicker(true)"
            @click="toggleTimePicker(true)"
        >
            <slot name="clock-icon" v-if="$slots['clock-icon']" />
            <ClockIcon v-if="!$slots['clock-icon']" />
        </div>
        <transition :name="transitionName(showTimePicker)" :css="showTransition">
            <div v-if="showTimePicker || timePicker" class="dp__overlay">
                <div class="dp__overlay_container">
                    <slot
                        name="time-picker-overlay"
                        v-if="$slots['time-picker-overlay']"
                        :range="range"
                        :hours="hours"
                        :minutes="minutes"
                        :seconds="seconds"
                        :set-hours="updateHours"
                        :set-minutes="updateMinutes"
                        :set-seconds="updateSeconds"
                    ></slot>
                    <template v-if="!$slots['time-picker-overlay']">
                        <div class="dp__overlay_row">
                            <TimeInput
                                v-for="(tInput, index) in timeInputs"
                                :key="index"
                                :disabled="index === 0 ? fixedStart : fixedEnd"
                                :hours="tInput.hours"
                                :minutes="tInput.minutes"
                                :seconds="tInput.seconds"
                                :filters="filters"
                                v-bind="{
                                    is24,
                                    hoursGridIncrement,
                                    minutesGridIncrement,
                                    secondsGridIncrement,
                                    hoursIncrement,
                                    minutesIncrement,
                                    secondsIncrement,
                                    filters,
                                    noHoursOverlay,
                                    noMinutesOverlay,
                                    noSecondsOverlay,
                                    enableSeconds,
                                }"
                                @update:hours="updateHours(getEvent($event, index, 'hours'))"
                                @update:minutes="updateMinutes(getEvent($event, index, 'minutes'))"
                                @update:seconds="updateSeconds(getEvent($event, index, 'seconds'))"
                            >
                                <template v-for="(slot, i) in timeInputSlots" #[slot]="args" :key="i">
                                    <slot :name="slot" v-bind="args" />
                                </template>
                            </TimeInput>
                        </div>
                    </template>
                    <div
                        v-if="!timePicker"
                        :class="toggleButtonClass"
                        role="button"
                        :aria-label="ariaLabels.closeTimePicker"
                        tabindex="0"
                        @keydown.enter="toggleTimePicker(false)"
                        @click="toggleTimePicker(false)"
                    >
                        <slot name="calendar-icon" v-if="$slots['calendar-icon']" />
                        <CalendarIcon v-if="!$slots['calendar-icon']" />
                    </div>
                </div>
            </div>
        </transition>
    </div>
</template>

<script lang="ts" setup>
    import { computed, inject, nextTick, onMounted, ref, useSlots } from 'vue';
    import type { PropType, ComputedRef } from 'vue';

    import { ClockIcon, CalendarIcon } from '@/components/Icons';
    import TimeInput from '@/components/TimePicker/TimeInput.vue';

    import { mapSlots } from '@/components/composition/slots';
    import { useTransitions } from '@/components/composition/transition';

    import type { IDateFilter, TimeInputRef, AreaLabels } from '@/interfaces';

    import { ariaLabelsKey, autoApplyKey, TimePickerProps } from '@/utils/props';

    const emit = defineEmits(['update:hours', 'update:minutes', 'update:seconds', 'mount', 'reset-flow']);

    const props = defineProps({
        ...TimePickerProps,
        range: { type: Boolean as PropType<boolean>, default: false },
        filters: { type: Object as PropType<IDateFilter>, default: () => ({}) },
        hours: { type: [Number, Array] as PropType<number | number[]>, default: 0 },
        minutes: { type: [Number, Array] as PropType<number | number[]>, default: 0 },
        seconds: { type: [Number, Array] as PropType<number | number[]>, default: 0 },
        customProps: { type: Object as PropType<Record<string, unknown>>, default: null },
    });
    const slots = useSlots();
    const autoApply = inject(autoApplyKey, false);
    const timeInputRef = ref<TimeInputRef | null>(null);
    const ariaLabels = inject<ComputedRef<AreaLabels>>(ariaLabelsKey);

    const { transitionName, showTransition } = useTransitions();

    onMounted(() => {
        emit('mount');
    });

    const showTimePicker = ref(false);

    const getTimeInput = (i: number) => {
        return {
            hours: Array.isArray(props.hours) ? props.hours[i] : props.hours,
            minutes: Array.isArray(props.minutes) ? props.minutes[i] : props.minutes,
            seconds: Array.isArray(props.seconds) ? props.seconds[i] : props.seconds,
        };
    };

    const timeInputs = computed((): { hours: number; minutes: number; seconds: number }[] => {
        const arr = [];
        if (props.range) {
            for (let i = 0; i < 2; i++) {
                arr.push(getTimeInput(i));
            }
        } else {
            arr.push(getTimeInput(0));
        }
        return arr;
    });

    const toggleTimePicker = (show: boolean, flow = false, childOpen = ''): void => {
        if (!flow) {
            emit('reset-flow');
        }
        showTimePicker.value = show;

        nextTick(() => {
            if (childOpen !== '' && timeInputRef.value) {
                timeInputRef.value.openChildCmp(childOpen);
            }
        });
    };

    const toggleButtonClass = computed(() => ({
        dp__button: true,
        dp__button_bottom: autoApply,
    }));

    const timeInputSlots = mapSlots(slots, 'timePicker');

    const getEvent = (event: number, index: number, property: 'hours' | 'minutes' | 'seconds') => {
        if (!props.range) {
            return event;
        }
        if (index === 0) {
            return [event, timeInputs.value[1][property]];
        }
        return [timeInputs.value[0][property], event];
    };

    const updateHours = (hours: number | number[]): void => {
        emit('update:hours', hours);
    };

    const updateMinutes = (minutes: number | number[]): void => {
        emit('update:minutes', minutes);
    };

    const updateSeconds = (seconds: number | number[]): void => {
        emit('update:seconds', seconds);
    };

    defineExpose({ toggleTimePicker });
</script>
