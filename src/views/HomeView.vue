<template>
  <div class="home">
    <div class="calendar-container">
      <h2>calendar</h2>
      <CalendarComponent
        :date="new Date()"
        :width="400"
        :height="200"
      ></CalendarComponent>
    </div>

    <div class="calendar-container">
      <h2>date picker</h2>
      <CalendarComponent
        :date="new Date()"
        type="pick"
        @pick-date="pickDate"
        :width="300"
        :height="300"
      ></CalendarComponent>
      <div>pick date: {{ ymd(date) }}</div>
    </div>

    <div class="calendar-container">
      <h2>date range picker</h2>
      <CalendarComponent
        :date="new Date()"
        type="range"
        @pick-date-range="pickDateRange"
        :width="300"
        :height="300"
      ></CalendarComponent>
      <div>
        picked date range : {{ ymd(dateRange.startDate) }} -
        {{ ymd(dateRange.endDate) }}
      </div>
    </div>
  </div>
</template>

<script>
import CalendarComponent from "@/components/CalendarComponent.vue";

export default {
  name: "HomeView",
  components: {
    CalendarComponent,
  },

  data() {
    return {
      date: undefined,
      dateRange: {
        startDate: undefined,
        endDate: undefined,
      },
    };
  },
  methods: {
    pickDate(date) {
      this.date = date;
    },
    pickDateRange(dateRange) {
      this.dateRange = dateRange;
    },
    ymd(_date) {
      if (!_date) return "";
      console.dir(_date);
      const year = _date.getFullYear();
      const month = _date.getMonth();
      const date = _date.getDate();

      return `${year}-${month}-${date}`;
    },
  },
};
</script>

<style scoped>
.home {
  width: 100%;
  display: flex;
  flex-direction: column;
  flex-wrap: wrap;
  align-items: center;
  justify-content: center;
}

.calendar-container {
  margin-bottom: 50px;
}

.calendar-container > div {
  margin-top: 20px;
}
</style>
