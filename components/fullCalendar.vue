<template>
  <div class="bg-white p-5 rounded-3xl">
    <div class="flex items-center justify-between">
      <h2 class="text-base p-5">
        {{
          locale === 'fa'
            ? months.fa[selectedMonth - 1]
            : months.en[selectedMonth - 1]
        }}
        ماه
        {{ selectedYear }}
      </h2>
      <div class="flex">
        <span @click="perMonth()">
          <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-6 h-6">
            <path stroke-linecap="round" stroke-linejoin="round" d="M8.25 4.5l7.5 7.5-7.5 7.5" />
          </svg>
        </span>
        <span @click="nextMonth()">
          <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-6 h-6">
            <path stroke-linecap="round" stroke-linejoin="round" d="M15.75 19.5L8.25 12l7.5-7.5" />
          </svg>
        </span>
      </div>
    </div>
    <div class="border-2 rounded-t-2xl">
      <div class="grid grid-cols-7 p-3 bg-purple-200 rounded-t-2xl">
        <div v-for="day in weekdays" :key="day.id" class="md:text-sm text-tiny-mobile-light text-center">
          {{ locale === 'fa' ? day.fa : day.en }}
        </div>
      </div>
      <div class="grid grid-cols-7">
        <div
          v-for="day in startMonth"
          :key="day + 100"
          class="md:p-10 border-r-2"
        ></div>
        <div
          v-for="day in dayOfMonth"
          :key="day"
          :class="hilightDate(day)"
          @click="dateSelected(day)"
        >
          {{ day }}
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    date: {
      type: Number,
      default: null,
      required: false,
    },
    selecetdDay: {
      type: Array,
      default: () => [],
      required: false,
    },
    selectedDates: {
      type: Array,
      default: () => [],
    },
    locale: {
      type: String,
      default: 'fa',
      required: false,
    },
    title: {
      type: String,
      default: 'تقویم',
      required: false,
    },
  },
  data() {
    return {
      weekdays: [
        { id: 1, fa: 'شنبه', en: 'Sun' },
        { id: 2, fa: 'یکشنبه', en: 'Mon' },
        { id: 3, fa: 'دوشنبه', en: 'Tue' },
        { id: 6, fa: 'سه‌شنبه', en: 'Wed' },
        { id: 4, fa: 'چهارشنبه', en: 'Thu' },
        { id: 5, fa: 'پنج‌شنبه', en: 'Fri' },
        { id: 7, fa: 'جمعه', en: 'Sat' },
      ],
      months: {
        fa: [
          'فروردین',
          'اردیبهشت',
          'خرداد',
          'تیر',
          'مرداد',
          'شهریور',
          'مهر',
          'آبان',
          'آذر',
          'دی',
          'بهمن',
          'اسفند',
        ],
        en: [
          'Jan',
          'Feb',
          'Mar',
          'Apr',
          'May',
          'June',
          'July',
          'Aug',
          'Sept',
          'Oct',
          'Nov',
          'Dec',
        ],
      },
      selectedMonth: this.$moment().format('jM'),
      selectedYear: this.$moment().format('jYYYY'),
    }
  },

  computed: {
    dayOfMonth() {
      if (this.locale === 'fa') {
        if (this.selectedMonth >= 1 && this.selectedMonth <= 6) {
          return 31
        } else if (this.selectedMonth >= 7 && this.selectedMonth <= 11) {
          return 30
        }
        return 29
      } else {
        return this.$moment().daysInMonth()
      }
    },

    startMonth() {
      const day = 1
      const year = this.selectedYear
      let dayStart = ''
      if (this.locale === 'fa') {
        const date = this.$moment(
          `${year}/${this.selectedMonth}/${day}`,
          'jYYYY/jM/jD'
        ).day()
        switch (date) {
          case 0:
            dayStart = 1
            break
          case 1:
            dayStart = 2
            break
          case 2:
            dayStart = 3
            break
          case 3:
            dayStart = 4
            break
          case 4:
            dayStart = 5
            break
          case 5:
            dayStart = 6
            break
          case 6:
            dayStart = 0
            break
        }
      } else {
        const date = this.$moment(
          `${year}/${this.selectedMonth}/${day}`,
          'YYYY/M/D'
        ).day()
        dayStart = date
      }
      return dayStart
    },
  },

  mounted() {
    if (this.locale === 'en') {
      this.selectedMonth = this.$moment().format('M')
      this.selectedYear = this.$moment().format('Y')
    }
  },

  methods: {
    perMonth() {
      if (this.selectedMonth === 1) {
        this.selectedYear = Number(this.selectedYear) - 1
        this.selectedMonth = 13
      }
      this.selectedMonth = Number(this.selectedMonth) - 1
    },

    nextMonth() {
      if (Number(this.selectedMonth) === 12) {
        this.selectedYear = Number(this.selectedYear) + 1
        this.selectedMonth = 0
      }
      this.selectedMonth = Number(this.selectedMonth) + 1
    },

    hilightDate(day) {
      const date = this.$moment(
        `${this.selectedYear}/${this.selectedMonth}/${day}`,
        'jYYYY/jM/jD'
      ).format('YYYY-MM-DD')
      const hilight = [
        'text-sm text-right pt-2 align-middle pt-1 px-1 md:p-10 p-3 border cursor-pointer day-item',
        this.selectedDates.includes(date)
          ? 'bg-purple-500 text-white hover:bg-purple-600'
          : 'bg-white ',
      ]
      return hilight
    },

    dateSelected(day) {
      const date = this.$moment(
        `${this.selectedYear}/${this.selectedMonth}/${day}`,
        'jYYYY/jM/jD'
      ).format('YYYY-MM-DD')
      this.$emit('dateSelected', date)
    },
  },
}
</script>

<style scoped>
.day-item:hover {
  background: #8b5cf6 !important;
  color: #fff !important;
}
</style>
