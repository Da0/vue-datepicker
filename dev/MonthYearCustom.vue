<template>
    <div class="month-year-wrapper" :data-year="years[year]?.text">
        {{ currentMonth }}
    </div>
</template>

<script>
    import { defineComponent } from 'vue';

    export default defineComponent({
        name: 'MonthYearCustom',
        // Available props
        props: {
            months: { type: Array, default: () => [] },
            years: { type: Array, default: () => [] },
            month: { type: Number, default: 0 },
            year: { type: Number, default: 0 },
            customProps: { type: Object, default: null },
        },

        computed: {
            currentMonth() {
                return this.months[this.month]?.text;
            },
        },
    });
</script>

<style lang="scss">
    .month-year-wrapper {
        font-size: 24px;
        font-weight: 600;
        color: gray;
        text-transform: capitalize;
    }
</style>
