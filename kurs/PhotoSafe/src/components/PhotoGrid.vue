<template>
  <div class="photo-grid-wrapper">
    <div v-if="groupedPhotos.length === 0" class="empty-state">
      <p>Фотографии не найдены или еще не загружены.</p>
    </div>

    <div v-for="group in groupedPhotos" :key="group.title" class="date-group">
      <h2 class="group-title">{{ group.title }}</h2>
      <div class="grid">
        <div 
          v-for="photo in group.photos" 
          :key="photo.id" 
          class="photo-item"
          :style="{ backgroundImage: 'url(' + photo.url + ')' }"
          @click="$emit('view', photo)"
        >
          <div class="photo-overlay">
            <div class="photo-info">
              <p class="photo-name">{{ photo.name }}</p>
              <p class="photo-date">{{ formatDate(photo.uploadDate) }}</p>
            </div>
            <button class="delete-btn" @click.stop="$emit('delete', photo.id)">
              <!-- Иконка удаления -->
              <svg class="icon" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                  <polyline points="3 6 5 6 21 6"></polyline>
                  <path d="M19 6v14a2 2 0 0 1-2 2H7a2 2 0 0 1-2-2V6m3 0V4a2 2 0 0 1 2-2h4a2 2 0 0 1 2 2v2"></path>
                  <line x1="10" y1="11" x2="10" y2="17"></line><line x1="14" y1="11" x2="14" y2="17"></line>
              </svg>
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { computed } from 'vue';

const props = defineProps({
  photos: {
    type: Array,
    required: true,
  }
});

defineEmits(['view', 'delete']);

// --- Логика группировки по дате ---
const groupedPhotos = computed(() => {
  const groups = {
    today: [],
    yesterday: [],
    lastWeek: [],
    thisMonth: [],
    lastMonth: [],
    longAgo: [],
  };

  const now = new Date();
  const today = new Date(now.getFullYear(), now.getMonth(), now.getDate());
  const yesterday = new Date(today);
  yesterday.setDate(yesterday.getDate() - 1);
  const startOfWeek = new Date(today);
  startOfWeek.setDate(startOfWeek.getDate() - now.getDay());
  const startOfMonth = new Date(now.getFullYear(), now.getMonth(), 1);
  const startOfLastMonth = new Date(now.getFullYear(), now.getMonth() - 1, 1);
  const endOfLastMonth = new Date(now.getFullYear(), now.getMonth(), 0);

  // Сортируем фото по дате от новых к старым
  const sortedPhotos = [...props.photos].sort((a, b) => new Date(b.uploadDate) - new Date(a.uploadDate));

  sortedPhotos.forEach(photo => {
    const photoDate = new Date(photo.uploadDate);
    const photoDay = new Date(photoDate.getFullYear(), photoDate.getMonth(), photoDate.getDate());

    if (photoDay.getTime() === today.getTime()) {
      groups.today.push(photo);
    } else if (photoDay.getTime() === yesterday.getTime()) {
      groups.yesterday.push(photo);
    } else if (photoDate >= startOfWeek) {
      groups.lastWeek.push(photo);
    } else if (photoDate >= startOfMonth) {
      groups.thisMonth.push(photo);
    } else if (photoDate >= startOfLastMonth && photoDate <= endOfLastMonth) {
      groups.lastMonth.push(photo);
    } else {
      groups.longAgo.push(photo);
    }
  });

  const groupOrder = [
    { title: 'Сегодня', key: 'today' },
    { title: 'Вчера', key: 'yesterday' },
    { title: 'На прошлой неделе', key: 'lastWeek' },
    { title: 'В этом месяце', key: 'thisMonth' },
    { title: 'В прошлом месяце', key: 'lastMonth' },
    { title: 'Давно', key: 'longAgo' },
  ];

  return groupOrder
    .map(group => ({
      title: group.title,
      photos: groups[group.key],
    }))
    .filter(group => group.photos.length > 0);
});

// --- Форматирование даты ---
function formatDate(date) {
  return new Date(date).toLocaleDateString('ru-RU');
}
</script>

<style scoped>
.date-group {
  margin-bottom: 40px;
}
.group-title {
  font-size: 1.5rem;
  margin-bottom: 20px;
  color: var(--secondary-color);
  border-bottom: 2px solid var(--border-color);
  padding-bottom: 5px;
}
.grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(250px, 1fr));
  gap: 20px;
}
.photo-item {
  aspect-ratio: 4 / 3;
  background-size: cover;
  background-position: center;
  background-color: #ccc; /* Фон для документов */
  border-radius: 8px;
  position: relative;
  overflow: hidden;
  cursor: pointer;
  box-shadow: 0 4px 8px rgba(0,0,0,0.1);
  transition: transform 0.2s ease-in-out, box-shadow 0.2s ease-in-out;
}
.photo-item:hover {
  transform: scale(1.03);
  box-shadow: 0 6px 12px rgba(0,0,0,0.15);
}
.photo-overlay {
  position: absolute;
  bottom: 0;
  left: 0;
  right: 0;
  background: linear-gradient(to top, rgba(0,0,0,0.8) 0%, rgba(0,0,0,0) 100%);
  color: var(--light-text);
  padding: 40px 15px 15px 15px;
  display: flex;
  justify-content: space-between;
  align-items: flex-end;
  opacity: 1;
  transition: opacity 0.2s ease-in-out;
}
.photo-info {
  max-width: calc(100% - 40px);
}
.photo-name {
  font-weight: bold;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}
.photo-date {
  font-size: 0.8rem;
  opacity: 0.8;
}
.delete-btn {
  position: absolute;
  top: 10px;
  right: 10px;
  background: rgba(255, 255, 255, 0.2);
  border: none;
  border-radius: 50%;
  width: 32px;
  height: 32px;
  color: white;
  cursor: pointer;
  display: flex;
  justify-content: center;
  align-items: center;
  opacity: 0;
  transition: opacity 0.2s, background-color 0.2s;
}
.photo-item:hover .delete-btn {
  opacity: 1;
}
.delete-btn:hover {
  background: rgba(231, 76, 60, 0.8); /* Красный при наведении */
}
.delete-btn .icon {
  width: 18px;
  height: 18px;
}

/* Иконка для документов */
.document-icon {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  width: 64px;
  height: 64px;
  color: var(--secondary-color);
  opacity: 0.5;
}
.document-icon .icon {
  width: 100%;
  height: 100%;
}

.empty-state {
  text-align: center;
  padding: 50px 0;
  color: #888;
  font-size: 1.2rem;
}
</style>