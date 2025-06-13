<template>
  <header class="app-header">
    <div class="header-content">
      <div class="logo">PhotoSafe</div>
      
      <div class="search-bar">
        <input 
          type="text" 
          placeholder="Поиск..."
          @input="$emit('search', $event.target.value)"
        >
      </div>

      <div class="header-actions">
        <label for="file-upload" class="upload-btn">
            <!-- Иконка с загрузкой -->
            <svg class="icon" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                <path d="M21 15v4a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2v-4"></path>
                <polyline points="17 8 12 3 7 8"></polyline>
                <line x1="12" y1="3" x2="12" y2="15"></line>
            </svg>
        </label>
        <input id="file-upload" type="file" @change="onFileChange" accept="image/*,application/pdf,.doc,.docx,.xls,.xlsx">
        
        <!-- Иконка пользователя -->
        <div class="user-icon">
             <svg class="icon" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                <path d="M20 21v-2a4 4 0 0 0-4-4H8a4 4 0 0 0-4 4v2"></path>
                <circle cx="12" cy="7" r="4"></circle>
            </svg>
        </div>
      </div>
    </div>
  </header>
</template>

<script setup>
const emit = defineEmits(['search', 'upload']);

function onFileChange(event) {
  const file = event.target.files[0];
  if (file) {
    emit('upload', file);
  }
  // Сбрасываю значение input, чтобы можно было загрузить тот же файл снова
  event.target.value = null; 
}
</script>

<style scoped>
.app-header {
  background-color: var(--primary-color);
  padding: 10px 0;
  box-shadow: 0 2px 4px rgba(0,0,0,0.1);
  position: sticky;
  top: 0;
  z-index: 100;
}

.header-content {
  width: 90%;
  max-width: 1400px;
  margin: 0 auto;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.logo {
  font-size: 1.8rem;
  font-weight: bold;
  color: var(--light-text);
}

.search-bar {
  flex-grow: 1;
  margin: 0 20px;
  max-width: 500px;
}

.search-bar input {
  width: 100%;
  padding: 8px 15px;
  border-radius: 20px;
  border: 1px solid transparent;
  background-color: rgba(255, 255, 255, 0.9);
  font-size: 1rem;
}
.search-bar input:focus {
  outline: none;
  border-color: var(--secondary-color);
}

.header-actions {
  display: flex;
  align-items: center;
  gap: 20px;
  color: var(--light-text);
}

.upload-btn {
  cursor: pointer;
  display: flex;
  align-items: center;
}
.upload-btn:hover {
  opacity: 0.8;
}

#file-upload {
  display: none;
}

.user-icon {
  background-color: rgba(255,255,255,0.3);
  border-radius: 50%;
  width: 36px;
  height: 36px;
  display: flex;
  justify-content: center;
  align-items: center;
}

/* Адаптивность */
@media (max-width: 768px) {
  .header-content {
    flex-wrap: wrap;
    justify-content: center;
    gap: 10px;
  }
  .search-bar {
    order: 3;
    width: 100%;
    margin: 0;
  }
  .logo {
    order: 1;
  }
  .header-actions {
    order: 2;
  }
}
</style>