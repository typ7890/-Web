<template>
  <div id="app-wrapper">
    <AppHeader 
      @search="updateSearchTerm" 
      @upload="handleUpload" 
    />

    <main class="container">
      <PhotoGrid 
        v-if="!currentPhoto"
        :photos="filteredPhotos" 
        @view="viewPhoto"
        @delete="deletePhoto"
      />
      
      <PhotoDetail
        v-else
        :photo="currentPhoto"
        @close="closePhotoView"
        @delete="deletePhotoAndClose"
      />
    </main>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue';
import AppHeader from './components/AppHeader.vue';
import PhotoGrid from './components/PhotoGrid.vue';
import PhotoDetail from './components/PhotoDetail.vue';

// --- Начальные данные для демонстрации ---
const getInitialPhotos = () => [
  { id: 1, name: 'Оклахома с высоты птичьего полёта', url: 'Анапа1.png', uploadDate: new Date() },
  { id: 2, name: 'Анапа с высоты птичьего полёта', url: 'Анапа2.png', uploadDate: new Date() },
  { id: 3, name: 'Оклахома Ночь', url: 'Оклахома.png', uploadDate: new Date(Date.now() - 86400000) },
  { id: 4, name: 'Анапа с высоты птичьего полёта', url: 'Анапа3.png', uploadDate: new Date(Date.now() - 5 * 86400000) },
  { id: 5, name: 'Анапа Парк аттракционов', url: 'Анапа4.png', uploadDate: new Date(Date.now() - 6 * 86400000) },
  { id: 6, name: 'Черноморье РЖД', url: 'ржд.png', uploadDate: new Date(Date.now() - 20 * 86400000) },
  { id: 7, name: 'Камчатский полуостров', url: 'остров.png', uploadDate: new Date(Date.now() - 45 * 86400000) },
  { id: 8, name: 'Акулий остров Австралия', url: 'Австралия.png', uploadDate: new Date(Date.now() - 100 * 86400000) },
  // Документы для разнообразия
  { id: 9, name: 'Максик', url: 'Максик.png', uploadDate: new Date(Date.now() - 86400000)},
  { id: 10, name: 'Серёжка', url: 'Серёжка.png', uploadDate: new Date(Date.now() - 160 * 86400000)},
];

// --- Реактивное состояние ---
const photos = ref(getInitialPhotos());
const searchTerm = ref('');
const currentPhoto = ref(null);

// --- Вычисляемые свойства ---
const filteredPhotos = computed(() => {
  if (!searchTerm.value) {
    return photos.value;
  }
  return photos.value.filter(photo =>
    photo.name.toLowerCase().includes(searchTerm.value.toLowerCase())
  );
});

// --- Методы ---
function updateSearchTerm(term) {
  searchTerm.value = term;
}

function viewPhoto(photo) {
  currentPhoto.value = photo;
}

function closePhotoView() {
  currentPhoto.value = null;
}

function deletePhoto(photoId) {
  photos.value = photos.value.filter(p => p.id !== photoId);
}

function deletePhotoAndClose(photoId) {
  deletePhoto(photoId);
  closePhotoView();
}

function handleUpload(file) {
  if (!file) return;

  const reader = new FileReader();
  reader.onload = (e) => {
    const newPhoto = {
      id: Date.now(), // Уникальный ID на основе времени
      name: file.name.split('.').slice(0, -1).join('.'), // Имя файла без расширения
      url: e.target.result, // base64 URL
      uploadDate: new Date(),
      isDocument: !file.type.startsWith('image/')
    };
    photos.value.unshift(newPhoto); // Добавляем в начало списка
  };
  reader.readAsDataURL(file);
}
</script>

<style>
/* Можно добавить стили для обертки, если нужно */
#app-wrapper {
  background-color: var(--bg-color);
}
</style>