<template>
  <div>
    <div class="flex items-center justify-between">
      <h2 class="text-base p-5">{{ title }}</h2>
      <div class="flex justify-between items-center">
        <span @click="perMonth()">
          <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-4 h-4">
            <path stroke-linecap="round" stroke-linejoin="round" d="M8.25 4.5l7.5 7.5-7.5 7.5" />
          </svg>
        </span>
        <div class="w-20 text-center">
          {{
          locale === 'fa'
            ? months.fa[selectedMonth - 1]
            : months.en[selectedMonth - 1]
        }}
        </div>
        <span @click="nextMonth()">
          <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-4 h-4">
            <path stroke-linecap="round" stroke-linejoin="round" d="M15.75 19.5L8.25 12l7.5-7.5" />
          </svg>
        </span>
      </div>
    </div>
    <div class="grid grid-cols-7 gap-2 m-3">
      <div
        v-for="day in weekdays"
        :key="day.id"
        class="text-tl-mobile text-center"
      >
        {{ locale === 'fa' ? day.fa : day.en }}
      </div>
    </div>
    <div class="grid grid-cols-7 gap-2 m-3">
      <div v-for="day in startMonth" :key="day + 100"></div>
      <div v-for="day in dayOfMonth" :key="day" :class="hilightdate(day)">
        {{day}}
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
    active: {
      type: Array,
      default: null,
      required: false,
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
        { id: 1, fa: 'ش', en: 'Sun' },
        { id: 2, fa: 'ی', en: 'Mon' },
        { id: 3, fa: 'د', en: 'Tue' },
        { id: 6, fa: 'س', en: 'Wed' },
        { id: 4, fa: 'چ', en: 'Thu' },
        { id: 5, fa: 'پ', en: 'Fri' },
        { id: 7, fa: 'ج', en: 'Sat' },
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
      activeDays: [],
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
    if (this.active) {
      for (let i = 0; i < this.active.length; i++) {
        if (this.locale === 'fa') {
          this.activeDays.push(parseInt(this.$moment(this.active[i]).format('jD')))
        }
        else {
          this.activeDays.push(parseInt(this.$moment(this.active[i]).format('D')))
        } 
      }
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
    hilightdate(day) {
      if (this.active === null) {
        return 'text-xs text-center rounded align-middle pt-1 px-1 bg-gray-200'
      }
      let month = Number(this.$moment(this.active[0]).format('jMM'))
      let year = Number(this.$moment(this.active[0]).format('jYYYY'))
      if (this.locale === 'en') {
        month = Number(this.$moment(this.active[0]).format('MM'))
        year = Number(this.$moment(this.active[0]).format('YYYY'))
      }
      
      return [
        'text-xs text-center rounded align-middle pt-1 px-1',
        this.activeDays.includes(parseInt(day)) && month === Number(this.selectedMonth) && year === Number(this.selectedYear)
          ? 'bg-purple-500 text-white' : 'bg-gray-200'
      ]
      
    },
  },
}
</script>

