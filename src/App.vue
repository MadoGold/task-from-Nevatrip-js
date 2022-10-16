<template>
  <div class="wrapper">
    <div>
      <select @change="updateRoute($event)" name="route" id="route">
        <option v-for="route in routes" :key="route.id" :value="route.value">{{ route.text }}</option>
      </select>
    </div>
    <div v-if="route === 'из A в B'">
      <label for="time1">Выберите время</label>
      <select @change="updateTime($event)" name="time1" id="time1">
        <option v-for="time in timeAB" :key="time.id" :value="time.value">{{ time.text }}</option>
      </select>
    </div>
    <div v-else-if="route === 'из B в A'">
      <label for="time2">Выберите время</label>
      <select @change="updateTime($event)" name="time2" id="time2">
        <option v-for="time in timeBA" :key="time.id" :value="time.value">{{ time.text }}</option>
      </select>
    </div>
    <div v-else>
      <label for="time3">Выберите время</label>
      <select @change="updateTime($event)" name="time3" id="time3">
        <option v-for="time in timeAB" :key="time.id" :value="time.value">{{ time.text }}</option>
      </select>
      <select @change="updateBackwardTime($event)"
        name="time4" id="time4">
        <option v-for="time in timeBA" :key="time.id" :value="time.value">{{ time.text }}</option>
      </select>
    </div>
    <div>
      <label for="num">Количество билетов</label>
      <input @input="updateNum($event)" id="num">
    </div>
    <div>
      <button @click="calcInfo">Посчитать</button>
    </div>
    <div v-if="price !== 0">
      <p>Вы выбрали {{ num }} билета по маршруту {{ route }} стоимостью {{ price }}р.</p>
      <p>Время в пути займет у вас {{ timeInTravel }} минут.</p>
      <p>Теплоход отправляется в {{ time.slice(0, 5) }}, а прибудет в
        {{ endTime.getHours() + 13 }}:{{
          endTime.getMinutes() < 10
            ? '0' + endTime.getMinutes()
            : endTime.getMinutes()
        }}.</p>
      <p v-if="route === 'из A в B и обратно в А'">
        Отправляется обратно в {{ backwardTime.slice(0, 5) }}, а прибудет в
        {{
          endBackTime.getHours() + 13 }}:{{
          endBackTime.getMinutes() < 10
            ? '0' + endBackTime.getMinutes()
            : endBackTime.getMinutes()
        }}.
      </p>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      routes: [
        {id: 0, value: 'из A в B', text: 'из A в B'},
        {id: 1, value: 'из B в A', text: 'из B в A'},
        {id: 3, value: 'из A в B и обратно в А', text: 'из A в B и обратно в А'},
      ],

      times: [
        {id: 0, value: '18:00(из A в B)', text: '18:00(из A в B)'},
        {id: 1, value: '18:30(из A в B)', text: '18:30(из A в B)'},
        {id: 2, value: '18:45(из A в B)', text: '18:45(из A в B)'},
        {id: 3, value: '19:00(из A в B)', text: '19:00(из A в B)'},
        {id: 4, value: '19:15(из A в B)', text: '19:15(из A в B)'},
        {id: 5, value: '21:00(из A в B)', text: '21:00(из A в B)'},
        {id: 6, value: '18:30(из B в A)', text: '18:30(из B в A)'},
        {id: 7, value: '18:45(из B в A)', text: '18:45(из B в A)'},
        {id: 8, value: '19:00(из B в A)', text: '19:00(из B в A)'},
        {id: 9, value: '19:15(из B в A)', text: '19:15(из B в A)'},
        {id: 10, value: '19:35(из B в A)', text: '19:35(из B в A)'},
        {id: 11, value: '21:50(из B в A)', text: '21:50(из B в A)'},
        {id: 12, value: '21:55(из B в A)', text: '21:55(из B в A)'},
      ],

      route: 'из A в B',
      time: '18:00(из A в B)',
      backwardTime: '19:00(из B в A)',
      endTime: '',
      endBackTime: '',
      num: 0,
      price: 0,
      timeInTravel: 0,
    }
  },
  methods: {
    updateRoute(e) {
      this.route = e.target.value
    },

    updateTime(e) {
      this.time = e.target.value
    },

    updateBackwardTime(e) {
      this.backwardTime = e.target.value
    },

    updateNum(e) {
      this.num = e.target.value
    },

    calcPrice() {
      this.price = this.num * (this.route === 'из A в B и обратно в А' ? 1200 : 700)
    },

    calcTimeInTravel() {
      this.timeInTravel = (this.route === 'из A в B и обратно в А' ? 100 : 50)
    },

    calcTimeEndTravel() {
      let endHour = Number(this.time.slice(0, 2))
      let endMinute = Number(this.time.slice(3, 5)) + 50

      while (endMinute >= 60) {
        endHour += 1
        endMinute -= 60
      }
      if (endMinute < 10) {
        endMinute = '0' + endMinute
      }

      this.endTime = new Date(Date.parse(`2012-01-26T${endHour}:${endMinute}:50.417-07:00`))
    },

    calcBackTimeEndTravel() {
      let endHour = Number(this.backwardTime.slice(0, 2))
      let endMinute = Number(this.backwardTime.slice(3, 5)) + 50

      while (endMinute >= 60) {
        endHour += 1
        endMinute -= 60
      }
      if (endMinute < 10) {
        endMinute = '0' + endMinute
      }

      this.endBackTime = new Date(Date.parse(`2012-01-26T${endHour}:${endMinute}:50.417-07:00`))
    },

    calcInfo() {
      this.calcPrice();
      this.calcTimeInTravel();
      this.calcTimeEndTravel();
      this.calcBackTimeEndTravel();
    }
  },

  computed: {
    timeAB() {
      return this.times.filter(time => time.value.includes('из A в B'))
    },
    timeBA() {
      if (this.route === 'из A в B и обратно в А') {
        const thisTime = Number(this.time.slice(0, 2)) * 60 + Number(this.time.slice(3, 5)) + 50
        return this.times.filter(t => {
          if (!t.value.includes('из B в A')) return false
          const tValue = Number(t.value.slice(0, 2)) * 60 + Number(t.value.slice(3, 5))
          return thisTime <= tValue
        })
      } else {
        return this.times.filter(time => time.value.includes('из B в A'))
      }
    }
  }
}
</script>

<style>
body {
  overflow: hidden;
  padding: 0;
  margin: 0;
}

#app {
  width: 100vw;
  height: 100vh;
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  background-color: cornsilk;
  color: #2c3e50;
}

.wrapper {
  width: 100vw;
  height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
  flex-direction: column;
  gap: 20px;
}

label {
  margin-right: 20px;
}

select {
  margin-right: 10px;
}
</style>
