<script setup>
import { ref } from 'vue';

// 1. 부모로부터 수신할 데이터(Props)
const data = defineProps({
    movie: {
        type: Object,
        required: true
    }
})

const draftMovie = ref(
  {
    title: "", rating: "0.0"
  }
);

const editMovie = () => {
  draftMovie.value.title = data.movie.title;
  draftMovie.value.rating = data.movie.rating;

  emit('edit-movie', data.movie.id);
}

const saveMovie = () => {
  if(draftMovie.value.title.trim()==="")
    draftMovie.value.title = data.movie.title;
  
  if(draftMovie.value.rating==="")
    draftMovie.value.rating = data.movie.rating;
  else if(draftMovie.value.rating>10.0)
    draftMovie.value.rating = 10.0
  else if(draftMovie.value.rating<0.0)
    draftMovie.value.rating = 0.0

  emit('save-movie', data.movie.id, {
    title: draftMovie.value.title.trim(),
    rating: draftMovie.value.rating
  });
}

const cancelMovie = () => {
  emit('cancel-movie', data.movie.id)
}

/*
옵션 C: 개별 영화 정보 수정(Edit) 기능 (난이도: 상)
● 요구사항: 영화 카드 내부에 [수정] 버튼을 추가하세요. 클릭 시 영화 제목과 평점이
텍스트에서 텍스트 입력창(<input>)으로 변신해야 합니다. 값을 고친 뒤 [저장] 버튼을
누르면 부모 데이터를 갱신(Emit)하고, 카드가 다시 원래 상태로 돌아오도록 구현하세요.
*/

// 2. 부모에게 발신할 이벤트(Emit) 명세
const emit = defineEmits(['like-movie', 'delete-movie', 'edit-movie', 'cancel-movie', 'save-movie']);

</script>
<template>
    <div class="card">
        <div class="poster-area">
            <img :src="data.movie.poster" :alt="data.movie.title" class="poster">
        </div>
        <div class="content-area">
          <template v-if="data.movie.isEditing">
            <!--편집 상태에서 보이는 모습-->
            <input v-model="draftMovie.title" @keyup.enter="saveMovie" @keyup.esc="cancelMovie" tabindex="1"
            class="edit-title" type="text" placeholder="영화 제목 입력...">
            <p class="rating">평점: 
              <input v-model="draftMovie.rating" @keyup.enter="saveMovie" @keyup.esc="cancelMovie" tabindex="2"
              class="edit-rating" type="number" 
              min="0" max="10" step="0.1" placeholder="10.0">
              / 10
            </p>
          </template>
          <template v-else>
            <!--편집 상태가 아닐 때(평소) 보이는 모습-->
            <h3>{{ data.movie.title }}</h3>
            <p class="rating">평점: {{ data.movie.rating }} / 10</p>
          </template>

          <p class="genre">장르: {{ data.movie.genre }}</p>
          <p class="likes">좋아요: {{ data.movie.likes }}</p>

          <div class="btn-group">
            <template v-if="data.movie.isEditing == false">
              <button class="btn like-btn" @click="$emit('like-movie', data.movie.id)">추천</button>
              <button class="btn edit-btn" @click="editMovie">수정</button>
              <button class="btn del-btn" @click="$emit('delete-movie', data.movie.id)">삭제</button>
            </template>
            <template v-else>
              <button class="btn save-btn" @click="saveMovie">저장</button>
              <button class="btn cancel-btn" @click="cancelMovie">취소</button>
            </template>
          </div>
        </div>
    </div>
</template>
<style scoped>
.card { 
  background: #fff; 
  border: 1px solid #e0e0e0; 
  border-radius: 12px; 
  overflow: hidden; 
  box-shadow: 0 4px 6px rgba(0,0,0,0.05); 
  transition: transform 0.2s ease;
}
.card:hover { 
  transform: translateY(-5px); 
  box-shadow: 0 8px 15px rgba(0,0,0,0.1); 
}
.poster-area { 
  position: relative; 
  width: 100%; 
  height: 300px; 
}
.poster { 
  width: 100%; 
  height: 100%; 
  object-fit: cover; 
}

.content-area {
  padding: 20px;
  text-align: center;
}
h3 { 
  margin: 0 0 10px 0; 
  color: #2c3e50; 
  font-size: 1.4rem;
}
.edit-title {
  margin: 0 0 0 0; 
  width: 180px;
  padding: 4px;
  font-size: 1.1rem;
  margin-bottom: 4px;
}
.edit-rating{
  width: 40px;
  border-color: #f39c12;
}
.rating { 
  font-weight: 600; 
  color: #f39c12; 
  margin: 5px 0;
}
.likes { 
  font-weight: 600; 
  color: #e74c3c; 
  margin: 5px 0 20px 0;
}
.genre{
  font-weight: 600; 
  color:#2c3e50;
  margin: 5px 0;
}

.btn-group { 
  display: flex; 
  gap: 10px; 
  justify-content: center; 
}
.btn { 
  padding: 10px 15px; 
  cursor: pointer; 
  border: none; 
  border-radius: 6px; 
  font-weight: bold; 
  font-size: 0.9rem;
  transition: opacity 0.2s;
}
.btn:hover { 
  opacity: 0.8; 
}
.like-btn { 
  background-color: #3498db; 
  color: white; 
  flex: 3;
}
.edit-btn { 
  background-color: #2ecc71; 
  color: white; 
  flex: 1;
}
.save-btn { 
  background-color: #2ecc71; 
  color: white; 
  flex: 1;
}
.cancel-btn { 
  background-color: #95a5a6; 
  color: white; 
  flex: 1;
}
.del-btn { 
  background-color: #e74c3c; 
  color: white; 
  flex: 1;
}
</style>