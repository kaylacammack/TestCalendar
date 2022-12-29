//CalendarMonth.vue
<template>
    <!--Parent container for the calendar calendar month-->
    <div class="calendar-month">

        <!-- //The calendar header -->
        <div class="calendar-month-header">
            <CalendarDateIndicator :selected-date="selectedDate" class="calendar-month-header-selected-month" />
            <CalendarDateSelector :current-date="today" :selected-date="selectedDate" @dateSelected="selectDate" />
        </div>

        <!-- //Calendar grid header -->
        <CalendarWeekdays />

        <!-- //Calendar grid -->
        <ol class="days-grid">
            <CalendarMonthDayItem v-for="day in days" :key="day.date" :day="day" :is-today="day.date === today" />
        </ol>
    </div>
</template>

<script>
import dayjs from "dayjs";
import weekday from "dayjs/plugin/weekday";
import weekOfYear from "dayjs/plugin/weekOfYear"
import CalendarDateIndicator from "./CalendarDateIndicator.vue";
import CalendarDateSelector from "./CalendarDateSelector.vue";
import CalendarMonthDayItem from "./CalendarMonthDayItem.vue";
import CalendarWeekdays from "./CalendarWeekdays.vue";

dayjs.extend(weekday);
dayjs.extend(weekOfYear);
export default {
    name: "CalendarMonth",

    components: {
        CalendarDateIndicator,
        CalendarDateSelector,
        CalendarWeekdays,
        CalendarMonthDayItem
    },
    data() {
        return {
            selectedDate: dayjs()
        };
    },

    computed: {
        days() {
            return [
                ...this.previousMonthDays,
                ...this.currentMonthDays,
                ...this.nextMonthDays
            ];
        },

        today() {
            return dayjs().format("YYYY-MM-DD");
        },

        month() {
            return Number(this.selectedDate.format("M"));
        },

        year() {
            return Number(this.selectedDate.format("YYYY"));
        },

        numberOfDaysInMonth() {
            return dayjs(this.selectedDate).daysInMonth();
        },

        currentMonthDays() {
            return [...Array(this.numberOfDaysInMonth)].map((day, index) => {
                return {
                    date: dayjs(`${this.year}-${this.month}-${index + 1}`).format(
                        "YYYY-MM-DD"
                    ),
                    isCurrentMonth: true
                };
            });
        },

        previousMonthDays() {
            const firstDayOfTheMonthWeekday = this.getWeekday(
                this.currentMonthDays[0].date
            );
            const previousMonth = dayjs(`${this.year}-${this.month}-01`).subtract(
                1,
                "month"
            );

            // Cover first day of the month being sunday (firstDayOfTheMonthWeekday === 0)
            const visibleNumberOfDaysFromPreviousMonth = firstDayOfTheMonthWeekday
                ? firstDayOfTheMonthWeekday - 1
                : 6;

            const previousMonthLastMondayDayOfMonth = dayjs(
                this.currentMonthDays[0].date
            )
                .subtract(visibleNumberOfDaysFromPreviousMonth, "day")
                .date();

            return [...Array(visibleNumberOfDaysFromPreviousMonth)].map(
                (day, index) => {
                    return {
                        date: dayjs(
                            `${previousMonth.year()}-${previousMonth.month() +
                            1}-${previousMonthLastMondayDayOfMonth + index}`
                        ).format("YYYY-MM-DD"),
                        isCurrentMonth: false
                    };
                }
            );
        },

        nextMonthDays() {
            const lastDayOfTheMonthWeekday = this.getWeekday(
                `${this.year}-${this.month}-${this.currentMonthDays.length}`
            );

            const nextMonth = dayjs(`${this.year}-${this.month}-01`).add(1, "month");

            const visibleNumberOfDaysFromNextMonth = lastDayOfTheMonthWeekday
                ? 7 - lastDayOfTheMonthWeekday
                : lastDayOfTheMonthWeekday;

            return [...Array(visibleNumberOfDaysFromNextMonth)].map((day, index) => {
                return {
                    date: dayjs(
                        `${nextMonth.year()}-${nextMonth.month() + 1}-${index + 1}`
                    ).format("YYYY-MM-DD"),
                    isCurrentMonth: false
                };
            });
        }
    },

    methods: {
        getWeekday(date) {
            return dayjs(date).weekday();
        },

        selectDate(newSelectedDate) {
            this.selectedDate = newSelectedDate;
        }
    }
};


</script>

<style scoped>
.calendar-month {
    position: relative;
    background-color: #EEEEEE;
    border: solid 1px #E0E0E0;
}

.calendar-month-header {
    display: flex;
    justify-content: space-between;
    background-color: #fff;
    padding: 10px;
}

.day-of-week {
    color: #3e4e63;
    font-size: 18px;
    background-color: #fff;
    padding-bottom: 5px;
    padding-top: 10px;
}

.day-of-week,
.days-grid {
    display: grid;
    grid-template-columns: repeat(7, 1fr);
}

.day-of-week>* {
    text-align: right;
    padding-right: 5px;
}

.days-grid {
    height: 100%;
    position: relative;
    grid-column-gap: 1px;
    grid-row-gap: 1px;
    border-top: solid 1px #cfd7e3;
}

ol,
li {
    padding: 0;
    margin: 0;
    list-style: none;
}
</style>