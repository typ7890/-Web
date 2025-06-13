<template>
  <div class="photo-detail-view">
    <div class="actions-bar">
        <button class="action-btn" @click="$emit('close')">
            <!-- Иконка назад -->
            <svg class="icon" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                <line x1="19" y1="12" x2="5" y2="12"></line><polyline points="12 19 5 12 12 5"></polyline>
            </svg>
            <span>Назад</span>
        </button>
        <div class="spacer"></div>
        <a :href="photo.url" :download="photo.name" class="action-btn">
            <!-- Иконка скачивания -->
            <svg class="icon" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                <path d="M21 15v4a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2v-4"></path>
                <polyline points="7 10 12 15 17 10"></polyline><line x1="12" y1="15" x2="12" y2="3"></line>
            </svg>
            <span>Скачать</span>
        </a>
        <button class="action-btn delete" @click="$emit('delete', photo.id)">
            <!-- Иконка удаления -->
            <svg class="icon" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                <polyline points="3 6 5 6 21 6"></polyline>
                <path d="M19 6v14a2 2 0 0 1-2 2H7a2 2 0 0 1-2-2V6m3 0V4a2 2 0 0 1 2-2h4a2 2 0 0 1 2 2v2"></path>
            </svg>
            <span>Удалить</span>
        </button>
    </div>

    <div class="image-container">
      <img :src="photo.url" :alt="photo.name">
    </div>

    <div class="photo-caption">
      <h2>{{ photo.name }}</h2>
      <p>Загружено: {{ new Date(photo.uploadDate).toLocaleString('ru-RU') }}</p>
    </div>
  </div>
</template>

<script setup>
defineProps({
  photo: {
    type: Object,
    required: true,
  }
});

defineEmits(['close', 'delete']);
</script>

<style scoped>
.photo-detail-view {
  display: flex;
  flex-direction: column;
  align-items: center;
  width: 100%;
}
.actions-bar {
  display: flex;
  width: 100%;
  padding: 10px;
  background-color: #fff;
  border-radius: 8px;
  box-shadow: 0 2px 5px rgba(0,0,0,0.1);
  margin-bottom: 20px;
  align-items: center;
}
.action-btn {
  background: none;
  border: none;
  cursor: pointer;
  display: flex;
  align-items: center;
  gap: 8px;
  padding: 8px 12px;
  border-radius: 6px;
  font-size: 1rem;
  color: var(--secondary-color);
  text-decoration: none;
  transition: background-color 0.2s;
}
.action-btn:hover {
  background-color: #f0f0f0;
}
.action-btn.delete:hover {
  background-color: #fce8e6;
  color: #c0392b;
}

.spacer {
  flex-grow: 1;
}

.image-container {
  max-width: 100%;
  margin-bottom: 20px;
  display: flex;
  justify-content: center;
}
.image-container img {
  max-width: 100%;
  max-height: 70vh;
  border-radius: 8px;
  box-shadow: 0 10px 20px rgba(0,0,0,0.15);
  object-fit: contain;
}

.photo-caption {
  text-align: center;
  color: var(--secondary-color);
}
.photo-caption h2 {
  margin-bottom: 5px;
}
.photo-caption p {
  color: #777;
}

@media (max-width: 600px) {
  .actions-bar span {
    display: none; /* Скрытие текста кнопок на маленьких экранах */
  }
  .action-btn {
    padding: 10px;
  }
}
</style>