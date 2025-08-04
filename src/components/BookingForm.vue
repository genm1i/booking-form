<script setup>
import BusyTimesPanel from './BusyTimesPanel.vue';
import { ref, watch, computed } from 'vue';
import BanquetForm from './BanquetForm.vue';

const booking = ref({
  date: '',
  time: '',
  count: 1,
  name: '',
  phone: '',
  email: '',
});

const availability = ref(null);
const error = ref('');
const confirmedBooking = ref([]);
const bookingSuccess = ref(false);
const today = new Date().toISOString().split('T')[0];

const availableTimes = [
  '12:00', '13:00', '14:00', '15:00',
  '16:00', '17:00', '18:00', '19:00',
  '20:00', '21:00', '22:00',
];

const bookings = [
  { date: '2025-08-04', time: '19:00', tableId: 1 },
  { date: '2025-08-04', time: '18:00', tableId: 1 },
  { date: '2025-08-05', time: '19:00', tableId: 1 },
  { date: '2025-08-19', time: '18:00', tableId: 5 },
  { date: '2025-08-20', time: '15:00', tableId: 9 }
];

watch(() => booking.value.count, (newval) => {
  if (newval <= 6) {
    error.value = '';
  }
});

const allBookings = computed(() => [
  ...bookings,
  ...confirmedBooking.value.map((b, index) => ({
    ...b,
    tableId: index + 100
  }))
]);

const busyTimes = computed(() => {
  if (!booking.value.date) return [];
  return allBookings.value
    .filter(b => b.date === booking.value.date)
    .map(b => b.time);
});

const checkAvailability = () => {
  const tables = [
    { id: 1, maxGuests: 4 },
    { id: 2, maxGuests: 4 },
    { id: 3, maxGuests: 4 },
    { id: 4, maxGuests: 4 },
    { id: 5, maxGuests: 4 },
    { id: 6, maxGuests: 6 },
    { id: 7, maxGuests: 6 },
    { id: 8, maxGuests: 6 },
    { id: 9, maxGuests: 6 },
    { id: 10, maxGuests: 6 },
  ];

  if (booking.value.count > 6) {
    error.value = 'Максимальное количество гостей — 6';
    availability.value = null;
    return;
  } else {
    error.value = '';
  }

  const suitableTables = tables.filter(table => table.maxGuests >= booking.value.count);

  const busyTableIds = allBookings.value
    .filter(b => b.date === booking.value.date && b.time === booking.value.time)
    .map(b => b.tableId);

  const available = suitableTables.some(table => !busyTableIds.includes(table.id));

  availability.value = available;
};

const confirmBooking = () => {
  if (!booking.value.name || !booking.value.phone || !booking.value.email) {
    error.value = 'Пожалуйста, заполните все контактные данные';
    return;
  }

  confirmedBooking.value.push({
    date: booking.value.date,
    time: booking.value.time,
    count: booking.value.count,
    name: booking.value.name,
    phone: booking.value.phone,
    email: booking.value.email,
  });
  bookingSuccess.value = true;
  booking.value = { date: '', time: '', count: 1, name: '', phone: '', email: '' };
  availability.value = null;
  error.value = '';
};
const showBanquetModal = ref(false);
</script>

<template>
  <div class="container">
    <form @submit.prevent="checkAvailability">
      <h1>Бронирование столика в кафе</h1>

      <div>
        <label for="date-booking">Дата бронирования</label>
        <input
          id="date-booking"
          type="date"
          v-model="booking.date"
          :min="today"
          required
        />
      </div>

      <div>
        <label for="time-booking">Время бронирования</label>
        <select id="time-booking" v-model="booking.time" required>
          <option disabled value="">Выберите время</option>
          <option 
            v-for="time in availableTimes" 
            :key="time" 
            :value="time"
            :disabled="busyTimes.includes(time)" 
          >
            {{ time }} <span v-if="busyTimes.includes(time)">(занято)</span>
          </option>
        </select>
      </div>

      <div>
        <label for="count">Количество гостей</label>
        <input
          id="count"
          type="number"
          v-model.number="booking.count"
          min="1"
          max="6"
          required
        />
      </div>

      <p v-if="error" style="color: red;">{{ error }}</p>
      <p v-if="availability === true" style="color: green;">Столик свободен</p>
      <p v-if="availability === false" style="color: red;">Столик занят</p>

      <div class="button-group">
        <button type="submit">Проверить доступность</button>
      </div>

      
      <div v-if="availability === true && !bookingSuccess" style="margin-top: 20px;">
        <h3>Контактные данные</h3>
        <div>
          <label for="name">Имя</label>
          <input id="name" type="text" v-model="booking.name" required />
        </div>
        <div>
          <label for="phone">Телефон</label>
          <input id="phone" type="tel" v-model="booking.phone" required />
        </div>
        <div>
          <label for="email">Email</label>
          <input id="email" type="email" v-model="booking.email" required />
        </div>
        <button type="button" @click="confirmBooking" class="button-group">Забронировать</button>
      </div>

      <p v-if="bookingSuccess" style="color: green; margin-top: 20px;">
        Бронирование подтверждено
      </p>

      <BusyTimesPanel :times="busyTimes" />
      
    <div class="banquet-toggle">
      <button @click="showBanquetModal = true">Хотите устроить банкет?</button>
    </div>

    <div v-if="showBanquetModal" class="modal-overlay" @click.self="showBanquetModal = false">
      <div class="modal-content">
        <button class="close-button" @click="showBanquetModal = false">×</button>
        <BanquetForm />
      </div>
    </div>
    </form>
  </div>
    

    
</template>


