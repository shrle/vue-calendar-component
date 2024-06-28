<template>
  <div class="calendar">
    <table>
      <caption>
        <div>
          <button @click="prevMonth">
            <span class="material-symbols-outlined"> chevron_left </span>
          </button>
          {{ year }}
          /
          {{ month }}
          <button @click="nextMonth">
            <span class="material-symbols-outlined"> chevron_right </span>
          </button>
        </div>
      </caption>
      <thead>
        <tr>
          <th>日</th>
          <th>月</th>
          <th>火</th>
          <th>水</th>
          <th>木</th>
          <th>金</th>
          <th>土</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="week in calendar" :key="week">
          <td
            v-for="(date, index) in week"
            :key="index"
            @click="pickDate(date, $event)"
            :class="{
              picked: isPickedDate(date),
              pickedStart: Date.parse(date) === Date.parse(rangeDate.startDate),
            }"
          >
            {{ getDate(date) }}
          </td>
        </tr>
      </tbody>
    </table>

    <menu
      class="pick-range-container"
      :class="{ 'visibility-visible': rangeButton.visibility }"
      :style="{
        left: rangeButtonPosition.x + 'px',
        top: rangeButtonPosition.y + 'px',
      }"
    >
      <button @click="setStartDate()">
        <span class="material-symbols-outlined"> start </span>
      </button>

      <button @click="setEndDate()">
        <span class="material-symbols-outlined"> keyboard_tab </span>
      </button>
    </menu>
  </div>
</template>

<script>
export default {
  name: "CalendarComponent",
  props: {
    date: Date,
    type: String,
    width: Number,
    height: Number,
  },
  mounted() {
    this.createCalendar(this.date);
  },
  data() {
    return {
      calendar: [],
      year: 0,
      month: 0,
      pickedDate: undefined,
      rangeDate: { startDate: undefined, endDate: undefined },
      rangeButton: { visibility: false },
      rangeButtonPosition: { x: 0, y: 0 },
    };
  },
  methods: {
    getDate(date) {
      if (date) {
        return date.getDate();
      }
      return "";
    },
    pickDate(date, event) {
      if (!date) return;
      this.pickedDate = date;
      if (this.type === "pick") {
        this.$emit("pick-date", date);
      } else if (this.type === "range") {
        this.rangeButton.visibility = true;
        const p = event.target.getBoundingClientRect();
        this.rangeButtonPosition = { x: p.x - 50, y: p.bottom + 10 };
      }
    },
    isPickedDate(date) {
      if (this.type === "pick" && date) {
        return this.pickedDate === date;
      } else if (this.type === "range" && date) {
        const start = this.rangeDate.startDate;
        const end = this.rangeDate.endDate;
        return (
          Date.parse(start) <= Date.parse(date) &&
          Date.parse(date) <= Date.parse(end)
        );
      }
    },

    setStartDate() {
      this.rangeDate.startDate = this.pickedDate;
      this.rangeButton.visibility = false;

      const start = this.rangeDate.startDate;
      const end = this.rangeDate.endDate;
      if (Date.parse(start) > Date.parse(end)) {
        this.rangeDate.endDate = undefined;
      }
      this.sendRangeDate();
    },
    setEndDate() {
      this.rangeDate.endDate = this.pickedDate;
      this.rangeButton.visibility = false;
      this.sendRangeDate();
    },
    sendRangeDate() {
      this.$emit("pick-range-date", this.rangeDate);
    },
    prevMonth() {
      if (this.month === 1) {
        this.month = 12;
        this.year--;
      } else {
        this.month--;
      }

      const date = new Date(
        `${this.year}-${this.month.toString(10).padStart(2, "0")}-01`
      );
      this.createCalendar(date);
    },
    nextMonth() {
      if (this.month === 12) {
        this.month = 1;
        this.year++;
      } else {
        this.month++;
      }

      const date = new Date(
        `${this.year}-${this.month.toString(10).padStart(2, "0")}-01`
      );
      this.createCalendar(date);
    },
    createCalendar(date) {
      let calendar = [];
      const year = date.getFullYear();
      const month = date.getMonth();
      this.year = year;
      this.month = month + 1;

      /**
       * 末日
       *
       *  month + 1 にしている理由は引数のdateを0にすることで"先月"の月末を取得するため
       * */
      const lastDate = new Date(year, month + 1, 0).getDate();
      /** 月の最初の日の曜日 */
      const startDay = new Date(year, month, 1).getDay();

      let dateCount = 1;
      for (let week = 0; week < 6; week++) {
        if (dateCount > lastDate) break;
        calendar[week] = [];
        for (let day = 0; day < 7; day++) {
          if ((week === 0 && day < startDay) || dateCount > lastDate) {
            calendar[week].push(undefined);
            continue;
          }
          calendar[week].push(new Date(year, month, dateCount));
          dateCount++;
        }
      }
      this.calendar = calendar;
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
* {
  margin: 0;
  padding: 0;
}

button {
  width: 40px;
  height: 40px;

  margin: 0 10px;
  border: 0;
  background: #ffffff;
  border-radius: 50%;
  display: flex;
  flex-wrap: wrap;
  align-items: center;
  justify-content: center;
}

button:hover {
  background: #dddddd;
}

button:active {
  background: #aaaaaa;
}
.pick-range-container {
  width: 200px;
  height: 50px;
  background-color: #ffffff;
  border: 0;
  border-radius: 50px;
  box-shadow: 0 0 10px 5px rgba(0, 0, 0, 0.1);
  display: flex;
  flex-direction: row;
  align-items: center;
  justify-content: center;

  visibility: hidden;
  position: fixed;
}

.visibility-visible {
  visibility: visible;
}

table {
  border-spacing: 0;
  border-collapse: collapse;
  width: v-bind(width + "px");
  height: v-bind(height + "px");
}
caption {
  caption-side: top;
  height: 40px;
}
caption > div {
  height: 40px;
  display: flex;
  flex-direction: row;
  align-items: center;
  justify-content: center;
}
td {
  border: #000000 1px solid;
}

td.picked {
  background: #ffaaaa;
}

td.pickedStart {
  border-left: #cc0000 2px solid;
  border-top: #cc0000 2px solid;
  background: #ffdddd;
}

td:first-child,
th:first-child {
  color: #cc0000;
}

td:last-child,
th:last-child {
  color: #0000cc;
}
</style>
