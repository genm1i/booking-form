<script setup>
import { ref } from 'vue';

const banquet = ref({
  date: '',
  time: '',
  guests: '',
  message: '',
  name: '',
  phone: '',
});
const success = ref(false);

const submitRequest = () => {
  // Простейшая валидация и вывод
  if (!banquet.value.name || !banquet.value.phone) return;
  success.value = true;
};
</script>

<template>
  <div>
    <h2 style="text-align: center; margin-bottom: 20px;">Заявка на банкет</h2>
    <form @submit.prevent="submitRequest">
      <div>
        <label>Дата</label>
        <input type="date" v-model="banquet.date" required />
      </div>
      <div>
        <label>Время</label>
        <input type="time" v-model="banquet.time" required />
      </div>
      <div>
        <label>Количество гостей</label>
        <input type="number" v-model="banquet.guests" min="1" required />
      </div>
      <div>
        <label>Дополнительная информация</label>
        <textarea v-model="banquet.message" rows="3" placeholder="Ваши пожелания..."></textarea>
      </div>
      <div>
        <label>Имя</label>
        <input type="text" v-model="banquet.name" required />
      </div>
      <div>
        <label>Телефон</label>
        <input type="tel" v-model="banquet.phone" required />
      </div>
      <button type="submit">Отправить заявку</button>
    </form>
    <p v-if="success" style="color: green; text-align: center; margin-top: 10px;">
      Заявка отправлена!
    </p>
  </div>
</template>

