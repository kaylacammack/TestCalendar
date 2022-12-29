//CalendarMonthDayItem.vue
<template>
    <li class="calendar-day" :class="{
    'calendar-day--not-current': !day.isCurrentMonth,
    'calendar-day--today': isToday
}">
        <span>{{ label }}</span>
    </li>
</template>

<script>
import dayjs from 'dayjs';

export default {
    name: "CalendarMonthDayItem",

    props: {
        day: {
            type: Object,
            required: true
        },

        isCurrentMonth: {
            type: Boolean,
            default: false
        },

        isToday: {
            type: Boolean,
            default: false
        }
    },

    computed: {
        label() {
            return dayjs(this.day.date).format("D");
        }
    }
};
</script>

<style scoped>
.calendar-day {
    position: relative;
    min-height: 100px;
    font-size: 16px;
    background-color: #fff;
    color: #424242;
    padding: 5px;
}

.calendar-day>span {
    display: flex;
    justify-content: center;
    align-items: center;
    position: absolute;
    right: 2px;
    width: 20px;
    height: 20px;
}

.calendar-day--not-current {
    background-color: #F5F5F5;
    color: #E0E0E0;
}

.calendar-day--today {
    padding-top: 4px;
}

.calendar-day--today>span {
    color: #fff;
    border-radius: 9999px;
    background-color: #424242;
}
</style>