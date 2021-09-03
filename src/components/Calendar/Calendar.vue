<!--
/**
 * @fileoverview Calendar component
 * @license MIT
 * @author Rafal Pospiech <neuronet.io@gmail.com>
 * @package GanttElastic
 */
-->
<template>
  <div
    class="gantt-elastic__calendar-wrapper"
    :style="{ ...root.style['calendar-wrapper'], width: root.state.options.width + 'px' }"
  >
    <div class="gantt-elastic__calendar" :style="{ ...root.style['calendar'], width: root.state.options.width + 'px' }">
      <calendar-row :items="dates.years" which="year" v-if="root.state.options.calendar.year.display"></calendar-row>
      <calendar-row :items="dates.quarters" which="quarter" v-if="root.state.options.calendar.quarter.display"></calendar-row>
      <calendar-row :items="dates.months" which="month" v-if="root.state.options.calendar.month.display"></calendar-row>
      <calendar-row :items="dates.weeks" which="week" v-if="root.state.options.calendar.week.display"></calendar-row>
      <calendar-row :items="dates.days" which="day" v-if="root.state.options.calendar.day.display"></calendar-row>
      <calendar-row :items="dates.hours" which="hour" v-if="root.state.options.calendar.hour.display"></calendar-row>
    </div>
  </div>
</template>

<script>
import dayjs from 'dayjs';
import CalendarRow from './CalendarRow.vue';

export default {
  name: 'Calendar',
  components: {
    CalendarRow
  },
  inject: ['root'],
  data() {
    return {};
  },

  methods: {
    /**
     * How many hours will fit?
     *
     * @returns {object}
     */
    howManyHoursFit(dayIndex) {
      const stroke = 1;
      const additionalSpace = stroke + 2;
      let fullCellWidth = this.root.state.options.times.steps[dayIndex].width.px;
      let formatNames = Object.keys(this.root.state.options.calendar.hour.format);
      for (let hours = 24; hours > 1; hours = Math.ceil(hours / 2)) {
        for (let formatName of formatNames) {
          if (
            (this.root.state.options.calendar.hour.maxWidths[formatName] + additionalSpace) * hours <= fullCellWidth &&
            hours > 1
          ) {
            return {
              count: hours,
              type: formatName
            };
          }
        }
      }
      return {
        count: 0,
        type: ''
      };
    },

    /**
     * How many days will fit?
     *
     * @returns {object}
     */
    howManyDaysFit() {
      const stroke = 1;
      const additionalSpace = stroke + 2;
      let fullWidth = this.root.state.options.width;
      let formatNames = Object.keys(this.root.state.options.calendar.day.format);
      for (let days = this.root.state.options.times.steps.length; days > 1; days = Math.ceil(days / 2)) {
        for (let formatName of formatNames) {
          if (
            (this.root.state.options.calendar.day.maxWidths[formatName] + additionalSpace) * days <= fullWidth &&
            days > 1
          ) {
            return {
              count: days,
              type: formatName
            };
          }
        }
      }
      return {
        count: 0,
        type: ''
      };
    },

    /**
     * How many weeks will fit?
     *
     * @returns {object}
     */
    howManyWeeksFit() {
        const stroke = 1;
        const additionalSpace = stroke + 2;
        let fullWidth = this.root.state.options.width;
        let formatNames = Object.keys(this.root.state.options.calendar.week.format);
        let currentWeek = dayjs(this.root.state.options.times.firstTime);
        let previousWeek = currentWeek.clone();
        const lastTime = this.root.state.options.times.lastTime;
        let weeksCount = this.root.weeksCount(
          this.root.state.options.times.firstTime,
          this.root.state.options.times.lastTime
        );
        if (weeksCount === 1) {
          for (let formatName of formatNames) {
            if (this.root.state.options.calendar.week.maxWidths[formatName] + additionalSpace <= fullWidth) {
              return {
                count: 1,
                type: formatName
              };
            }
          }
        }
        for (let weeks = weeksCount; weeks > 1; weeks = Math.ceil(weeks / 2)) {
          for (let formatName of formatNames) {
            if (
              (this.root.state.options.calendar.week.maxWidths[formatName] + additionalSpace) * weeks <= fullWidth &&
              weeks > 1
            ) {
              return {
                count: weeks,
                type: formatName
              };
            }
          }
        }
        return {
          count: 0,
          type: formatNames[0]
        };
      },

    /**
     * How many months will fit?
     *
     * @returns {object}
     */
    howManyMonthsFit() {
      const stroke = 1;
      const additionalSpace = stroke + 2;
      let fullWidth = this.root.state.options.width;
      let formatNames = Object.keys(this.root.state.options.calendar.month.format);
      let currentMonth = dayjs(this.root.state.options.times.firstTime);
      let previousMonth = currentMonth.clone();
      const lastTime = this.root.state.options.times.lastTime;
      let monthsCount = this.root.monthsCount(
        this.root.state.options.times.firstTime,
        this.root.state.options.times.lastTime
      );
      if (monthsCount === 1) {
        for (let formatName of formatNames) {
          if (this.root.state.options.calendar.month.maxWidths[formatName] + additionalSpace <= fullWidth) {
            return {
              count: 1,
              type: formatName
            };
          }
        }
      }
      for (let months = monthsCount; months > 1; months = Math.ceil(months / 2)) {
        for (let formatName of formatNames) {
          if (
            (this.root.state.options.calendar.month.maxWidths[formatName] + additionalSpace) * months <= fullWidth &&
            months > 1
          ) {
            return {
              count: months,
              type: formatName
            };
          }
        }
      }
      return {
        count: 0,
        type: formatNames[0]
      };
    },

    /**
     * How many quarters will fit?
     *
     * @returns {object}
     */
    howManyQuartersFit() {
      const stroke = 1;
      const additionalSpace = stroke + 2;
      let fullWidth = this.root.state.options.width;
      let formatNames = Object.keys(this.root.state.options.calendar.quarter.format);
      let currentquarter = dayjs(this.root.state.options.times.firstTime);
      let previousquarter = currentquarter.clone();
      const lastTime = this.root.state.options.times.lastTime;
      let quartersCount = this.root.quartersCount(
        this.root.state.options.times.firstTime,
        this.root.state.options.times.lastTime
      );
      if (quartersCount === 1) {
        for (let formatName of formatNames) {
          if (this.root.state.options.calendar.quarter.maxWidths[formatName] + additionalSpace <= fullWidth) {
            return {
              count: 1,
              type: formatName
            };
          }
        }
      }
      for (let quarters = quartersCount; quarters > 1; quarters = Math.ceil(quarters / 2)) {
        for (let formatName of formatNames) {
          if (
            (this.root.state.options.calendar.quarter.maxWidths[formatName] + additionalSpace) * quarters <= fullWidth &&
            quarters > 1
          ) {
            return {
              count: quarters,
              type: formatName
            };
          }
        }
      }
      return {
        count: 0,
        type: formatNames[0]
      };
    },

    /**
     * How many years will fit?
     *
     * @returns {object}
     */
    howManyYearsFit() {
      const stroke = 1;
      const additionalSpace = stroke + 2;
      let fullWidth = this.root.state.options.width;
      let formatNames = Object.keys(this.root.state.options.calendar.year.format);
      let currentYear = dayjs(this.root.state.options.times.firstTime);
      let previousYear = currentYear.clone();
      const lastTime = this.root.state.options.times.lastTime;
      let yearsCount = this.root.yearsCount(
        this.root.state.options.times.firstTime,
        this.root.state.options.times.lastTime
      );
      if (yearsCount === 1) {
        for (let formatName of formatNames) {
          if (this.root.state.options.calendar.year.maxWidths[formatName] + additionalSpace <= fullWidth) {
            return {
              count: 1,
              type: formatName
            };
          }
        }
      }
      for (let years = yearsCount; years > 1; years = Math.ceil(years / 2)) {
        for (let formatName of formatNames) {
          if (
            (this.root.state.options.calendar.year.maxWidths[formatName] + additionalSpace) * years <= fullWidth &&
            years > 1
          ) {
            return {
              count: years,
              type: formatName
            };
          }
        }
      }
      return {
        count: 0,
        type: formatNames[0]
      };
    },

    /**
     * Generate hours
     *
     * @returns {array}
     */
    generateHours() {
      let allHours = [];
      if (!this.root.state.options.calendar.hour.display) {
        return allHours;
      }
      const steps = this.root.state.options.times.steps;
      const localeName = this.root.state.options.locale.name;
      for (let hourIndex = 0, len = steps.length; hourIndex < len; hourIndex++) {
        const hoursCount = this.howManyHoursFit(hourIndex);
        if (hoursCount.count === 0) {
          continue;
        }
        const hours = { key: hourIndex + 'step', children: [] };
        const hourStep = 24 / hoursCount.count;
        const hourWidthPx = steps[hourIndex].width.px / hoursCount.count;
        for (let i = 0, len = hoursCount.count; i < len; i++) {
          const hour = i * hourStep;
          let index = hourIndex;
          if (hourIndex > 0) {
            index = hourIndex - Math.floor(hourIndex / 24) * 24;
          }
          let textWidth = 0;
          if (typeof this.root.state.options.calendar.hour.widths[index] !== 'undefined') {
            textWidth = this.root.state.options.calendar.hour.widths[index][hoursCount.type];
          }
          let x = steps[hourIndex].offset.px + hourWidthPx * i;
          hours.children.push({
            index: hourIndex,
            key: 'h' + i,
            x,
            y: this.root.state.options.calendar.day.height + this.root.state.options.calendar.month.height,
            width: hourWidthPx,
            textWidth,
            height: this.root.state.options.calendar.hour.height,
            label: this.root.state.options.calendar.hour.formatted[hoursCount.type][hour]
          });
        }
        allHours.push(hours);
      }
      return allHours;
    },

    /**
     * Generate days
     *
     * @returns {array}
     */
    generateDays() {
      let days = [];
      if (!this.root.state.options.calendar.day.display) {
        return days;
      }
      const daysCount = this.howManyDaysFit();
      if (daysCount.count === 0) {
        return days;
      }
      const steps = this.root.state.options.times.steps;
      const localeName = this.root.state.options.locale.name;
      const dayStep = Math.ceil(steps.length / daysCount.count);
      for (let dayIndex = 0, len = steps.length; dayIndex < len; dayIndex += dayStep) {
        let dayWidthPx = 0;
        // day could be shorter (daylight saving time) so join widths and divide
        for (let currentStep = 0; currentStep < dayStep; currentStep++) {
          if (typeof steps[dayIndex + currentStep] !== 'undefined') {
            dayWidthPx += steps[dayIndex + currentStep].width.px;
          }
        }
        const date = dayjs(steps[dayIndex].time);
        let textWidth = 0;
        if (typeof this.root.state.options.calendar.day.widths[dayIndex] !== 'undefined') {
          textWidth = this.root.state.options.calendar.day.widths[dayIndex][daysCount.type];
        }
        let x = steps[dayIndex].offset.px;
        const label = this.root.state.options.calendar.day.format[daysCount.type](date.locale(localeName));
        days.push({
          index: dayIndex,
          key: steps[dayIndex].time + 'd',
          x,
          y: this.root.state.options.calendar.month.height,
          width: dayWidthPx,
          textWidth,
          height: this.root.state.options.calendar.day.height,
          label
        });
      }
      return days.map(item => ({
        key: item.key,
        children: [item]
      }));
    },

    /**
     * Generate weeks
     *
     * @returns {array}
     */
    generateWeeks() {
      let weeks = [];
      if (!this.root.state.options.calendar.week.display) {
        return weeks;
      }
      const weeksCount = this.howManyWeeksFit();
      if (weeksCount.count === 0) {
        return weeks;
      }
      const steps = this.root.state.options.times.steps;
      const localeName = this.root.state.options.locale.name;
      let formatNames = Object.keys(this.root.state.options.calendar.week.format);
      let currentDate = dayjs(this.root.state.options.times.firstTime);
      for (let weekIndex = 0; weekIndex < weeksCount.count; weekIndex++) {
        let weekWidth = 0;
        let weekOffset = Number.MAX_SAFE_INTEGER;
        let finalDate = dayjs(currentDate)
          .add(1, 'week')
          .startOf('week');
        if (finalDate.valueOf() > this.root.state.options.times.lastTime) {
          finalDate = dayjs(this.root.state.options.times.lastTime);
        }
        // we must find first and last step to get the offsets / widths
        for (let step = 0, len = this.root.state.options.times.steps.length; step < len; step++) {
          let currentStep = this.root.state.options.times.steps[step];
          if (currentStep.time >= currentDate.valueOf() && currentStep.time < finalDate.valueOf()) {
            weekWidth += currentStep.width.px;
            if (currentStep.offset.px < weekOffset) {
              weekOffset = currentStep.offset.px;
            }
          }
        }
        let label = '';
        let choosenFormatName;
        for (let formatName of formatNames) {
          if (this.root.state.options.calendar.week.maxWidths[formatName] + 2 <= weekWidth) {
            const formatFunction = this.root.state.options.calendar.week.format[formatName];
            const dateLocal = currentDate.locale(localeName);
            label = formatFunction(dateLocal);
            choosenFormatName = formatName;
          }
        }
        let textWidth = 0;
        if (typeof this.root.state.options.calendar.week.widths[weekIndex] !== 'undefined') {
          textWidth = this.root.state.options.calendar.week.widths[weekIndex][choosenFormatName];
        }
        let x = weekOffset;
        weeks.push({
          index: weekIndex,
          key: weekIndex + 'm',
          x,
          y: 0,
          width: weekWidth,
          textWidth,
          choosenFormatName,
          height: this.root.state.options.calendar.week.height,
          label
        });
        currentDate = currentDate.add(1, 'week').startOf('week');
        if (currentDate.valueOf() > this.root.state.options.times.lastTime) {
          currentDate = dayjs(this.root.state.options.times.lastTime);
        }
      }
      return weeks.map(item => ({
        key: item.key,
        children: [item]
      }));
    },

    /**
     * Generate months
     *
     * @returns {array}
     */
    generateMonths() {
      let months = [];
      if (!this.root.state.options.calendar.month.display) {
        return months;
      }
      const monthsCount = this.howManyMonthsFit();
      if (monthsCount.count === 0) {
        return months;
      }
      const steps = this.root.state.options.times.steps;
      const localeName = this.root.state.options.locale.name;
      let formatNames = Object.keys(this.root.state.options.calendar.month.format);
      let currentDate = dayjs(this.root.state.options.times.firstTime);
      for (let monthIndex = 0; monthIndex < monthsCount.count; monthIndex++) {
        let monthWidth = 0;
        let monthOffset = Number.MAX_SAFE_INTEGER;
        let finalDate = dayjs(currentDate)
          .add(1, 'month')
          .startOf('month');
        if (finalDate.valueOf() > this.root.state.options.times.lastTime) {
          finalDate = dayjs(this.root.state.options.times.lastTime);
        }
        // we must find first and last step to get the offsets / widths
        for (let step = 0, len = this.root.state.options.times.steps.length; step < len; step++) {
          let currentStep = this.root.state.options.times.steps[step];
          if (currentStep.time >= currentDate.valueOf() && currentStep.time < finalDate.valueOf()) {
            monthWidth += currentStep.width.px;
            if (currentStep.offset.px < monthOffset) {
              monthOffset = currentStep.offset.px;
            }
          }
        }
        let label = '';
        let choosenFormatName;
        for (let formatName of formatNames) {
          if (this.root.state.options.calendar.month.maxWidths[formatName] + 2 <= monthWidth) {
            label = this.root.state.options.calendar.month.format[formatName](currentDate.locale(localeName));
            choosenFormatName = formatName;
          }
        }
        let textWidth = 0;
        if (typeof this.root.state.options.calendar.month.widths[monthIndex] !== 'undefined') {
          textWidth = this.root.state.options.calendar.month.widths[monthIndex][choosenFormatName];
        }
        let x = monthOffset;
        months.push({
          index: monthIndex,
          key: monthIndex + 'm',
          x,
          y: 0,
          width: monthWidth,
          textWidth,
          choosenFormatName,
          height: this.root.state.options.calendar.month.height,
          label
        });
        currentDate = currentDate.add(1, 'month').startOf('month');
        if (currentDate.valueOf() > this.root.state.options.times.lastTime) {
          currentDate = dayjs(this.root.state.options.times.lastTime);
        }
      }
      return months.map(item => ({
        key: item.key,
        children: [item]
      }));
    },

    /**
     * Generate quarters
     *
     * @returns {array}
     */
    generateQuarters() {
      let quarters = [];
      if (!this.root.state.options.calendar.quarter.display) {
        return quarters;
      }
      const quartersCount = this.howManyQuartersFit();
      if (quartersCount.count === 0) {
        return quarters;
      }
      const steps = this.root.state.options.times.steps;
      const localeName = this.root.state.options.locale.name;
      let formatNames = Object.keys(this.root.state.options.calendar.quarter.format);
      let currentDate = dayjs(this.root.state.options.times.firstTime);
      for (let quarterIndex = 0; quarterIndex < quartersCount.count; quarterIndex++) {
        let quarterWidth = 0;
        let quarterOffset = Number.MAX_SAFE_INTEGER;
        let finalDate = dayjs(currentDate)
          .add(1, 'quarter')
          .startOf('quarter');
        if (finalDate.valueOf() > this.root.state.options.times.lastTime) {
          finalDate = dayjs(this.root.state.options.times.lastTime);
        }
        // we must find first and last step to get the offsets / widths
        for (let step = 0, len = this.root.state.options.times.steps.length; step < len; step++) {
          let currentStep = this.root.state.options.times.steps[step];
          if (currentStep.time >= currentDate.valueOf() && currentStep.time < finalDate.valueOf()) {
            quarterWidth += currentStep.width.px;
            if (currentStep.offset.px < quarterOffset) {
              quarterOffset = currentStep.offset.px;
            }
          }
        }
        let label = '';
        let choosenFormatName;
        for (let formatName of formatNames) {
          if (this.root.state.options.calendar.quarter.maxWidths[formatName] + 2 <= quarterWidth) {
            const format = this.root.state.options.calendar.quarter.format[formatName];
            const day = currentDate.locale(localeName);
            label = format(day);
            choosenFormatName = formatName;
          }
        }
        let textWidth = 0;
        if (typeof this.root.state.options.calendar.quarter.widths[quarterIndex] !== 'undefined') {
          textWidth = this.root.state.options.calendar.quarter.widths[quarterIndex][choosenFormatName];
        }
        let x = quarterOffset;
        quarters.push({
          index: quarterIndex,
          key: quarterIndex + 'm',
          x,
          y: 0,
          width: quarterWidth,
          textWidth,
          choosenFormatName,
          height: this.root.state.options.calendar.quarter.height,
          label
        });
        currentDate = currentDate.add(1, 'quarter').startOf('quarter');
        if (currentDate.valueOf() > this.root.state.options.times.lastTime) {
          currentDate = dayjs(this.root.state.options.times.lastTime);
        }
      }
      return quarters.map(item => ({
        key: item.key,
        children: [item]
      }));
    },

    /**
     * Generate years
     *
     * @returns {array}
     */
    generateYears() {
      let years = [];
      if (!this.root.state.options.calendar.year.display) {
        return years;
      }
      const yearsCount = this.howManyYearsFit();
      if (yearsCount.count === 0) {
        return years;
      }
      const steps = this.root.state.options.times.steps;
      const localeName = this.root.state.options.locale.name;
      let formatNames = Object.keys(this.root.state.options.calendar.year.format);
      let currentDate = dayjs(this.root.state.options.times.firstTime);
      for (let yearIndex = 0; yearIndex < yearsCount.count; yearIndex++) {
        let yearWidth = 0;
        let yearOffset = Number.MAX_SAFE_INTEGER;
        let finalDate = dayjs(currentDate)
          .add(1, 'year')
          .startOf('year');
        if (finalDate.valueOf() > this.root.state.options.times.lastTime) {
          finalDate = dayjs(this.root.state.options.times.lastTime);
        }
        // we must find first and last step to get the offsets / widths
        for (let step = 0, len = this.root.state.options.times.steps.length; step < len; step++) {
          let currentStep = this.root.state.options.times.steps[step];
          if (currentStep.time >= currentDate.valueOf() && currentStep.time < finalDate.valueOf()) {
            yearWidth += currentStep.width.px;
            if (currentStep.offset.px < yearOffset) {
              yearOffset = currentStep.offset.px;
            }
          }
        }
        let label = '';
        let choosenFormatName;
        for (let formatName of formatNames) {
          if (this.root.state.options.calendar.year.maxWidths[formatName] + 2 <= yearWidth) {
            label = this.root.state.options.calendar.year.format[formatName](currentDate.locale(localeName));
            choosenFormatName = formatName;
          }
        }
        let textWidth = 0;
        if (typeof this.root.state.options.calendar.year.widths[yearIndex] !== 'undefined') {
          textWidth = this.root.state.options.calendar.year.widths[yearIndex][choosenFormatName];
        }
        let x = yearOffset;
        years.push({
          index: yearIndex,
          key: yearIndex + 'm',
          x,
          y: 0,
          width: yearWidth,
          textWidth,
          choosenFormatName,
          height: this.root.state.options.calendar.year.height,
          label
        });
        currentDate = currentDate.add(1, 'year').startOf('year');
        if (currentDate.valueOf() > this.root.state.options.times.lastTime) {
          currentDate = dayjs(this.root.state.options.times.lastTime);
        }
      }
      return years.map(item => ({
        key: item.key,
        children: [item]
      }));
    },

    /**
     * Sum all calendar rows height and return result
     *
     * @returns {int}
     */
    calculateCalendarDimensions({ hours, days, weeks, months, quarters, years }) {
      let height = 0;
      if (this.root.state.options.calendar.hour.display && hours.length > 0) {
        height += this.root.state.options.calendar.hour.height;
      }
      if (this.root.state.options.calendar.day.display && days.length > 0) {
        height += this.root.state.options.calendar.day.height;
      }
      if (this.root.state.options.calendar.week.display && weeks.length > 0) {
        height += this.root.state.options.calendar.week.height;
      }
      if (this.root.state.options.calendar.month.display && months.length > 0) {
        height += this.root.state.options.calendar.month.height;
      }
      if (this.root.state.options.calendar.quarter.display && quarters.length > 0) {
        height += this.root.state.options.calendar.quarter.height;
      }
      if (this.root.state.options.calendar.year.display && years.length > 0) {
        height += this.root.state.options.calendar.year.height;
      }
      this.root.state.options.calendar.height = height;
    }
  },

  computed: {
    dates() {
      const hours = this.generateHours();
      const days = this.generateDays();
      const weeks = this.generateWeeks();
      const months = this.generateMonths();
      const quarters = this.generateQuarters();
      const years = this.generateYears();
      const allDates = { hours, days, weeks, months, quarters, years };
      this.calculateCalendarDimensions(allDates);
      return allDates;
    }
  }
};
</script>
