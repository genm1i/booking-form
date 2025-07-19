<script setup>
import { ref, watch } from 'vue';

const booking = ref({
  date: '',
  time: '',
  count: 1,
});

const availability = ref(null);
const error = ref('');
const confirmedBooking = ref([]);
const bookingSuccess = ref(false);
const today = new Date().toISOString().split('T')[0]; 

watch(() => booking.value.count, (newval) => {
    if(newval <= 6) {
        error.value = '';
    }
});

const availableTimes = [
  '12:00', '13:00', '14:00', '15:00',
  '16:00', '17:00', '18:00', '19:00',
  '20:00', '21:00', '22:00',
];



const checkAvailability = () => {
    const tables = [
    {id: 1, maxGuests: 4 },
    {id: 2, maxGuests: 4 },
    {id: 3, maxGuests: 4 },
    {id: 4, maxGuests: 4 },
    {id: 5, maxGuests: 4 },
    {id: 6, maxGuests: 6 },
    {id: 7, maxGuests: 6 },
    {id: 8, maxGuests: 6 },
    {id: 9, maxGuests: 6 },
    {id: 10, maxGuests: 6 },
];

  const bookings = [
    { date: '2025-07-18', time: '19:00', tableId: 1 },
    { date: '2025-07-19', time: '18:00', tableId: 5 },
    { date: '2025-07-19', time: '15:00', tableId: 9 }
  ];

if (booking.value.count > 6) {
    error.value = 'Максимальное количество гостей - 6';
    availability.value = null;
    return;
} else {
    error.value = '';
}

  const suitableTables = tables.filter(table => table.maxGuests >= booking.value.count);

  const busyTableIds = bookings
  .filter(b => b.date === booking.value.date && b.time === booking.value.time)
  .map(b=> b.tableId);

  const available = suitableTables.some(table => !busyTableIds.includes(table.id));

  availability.value = available;
};

const confirmBooking = () => {
        confirmedBooking.value.push({
            date: booking.value.date,
            time: booking.value.time,
            count: booking.value.count
        })
        bookingSuccess.value = true;
        booking.value = {date: '', time: '', count: 1};
        availability.value = null;
};

</script>

<template>
  <div class="container">
    <form @submit.prevent="checkAvailability">
      <h1>Бронирование столика в кафе</h1>
      <div>
        <label for="date-booking">Дата бронирования</label>
        <input id="date-booking" type="date" v-model="booking.date" :min="today" required />
      </div>
      <div>
        <label for="time-booking">Время бронирования</label>
        <select id="time-booking" v-model="booking.time" required>
          <option disabled value="">Выберите время</option>
          <option v-for="time in availableTimes" :key="time" value="time">{{ time }}</option>
        </select>
      </div>
      <div>
        <label for="count">Количество гостей</label>
        <input id="count" type="number" v-model.number="booking.count" min="1" max="6"/>
        <p v-if="error" style="color: red;">{{ error }}</p>
        <p v-if="availability === true" style="color: green;">Столик свободен</p>
        <p v-if="availability === false" style="color: red;">Столик занят</p>
      </div>
      <div class="button-group">
        <button type="submit">Проверить доступность</button>
        <button v-if="availability && !bookingSuccess" type="button" @click="confirmBooking">Забронировать</button>
      </div>
      <p v-if="bookingSuccess" style="color: green;">Бронирование подтверждено</p>
    </form>
  </div>
</template>
