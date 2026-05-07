<script setup>
import { ref } from 'vue';
import MovieCard from './components/MovieCard.vue';

const movies = ref([
  {
    id: 1, title: '인셉션', rating: 9.5, likes: 0, genre: 'SF', isEditing: false,
    poster: 'https://picsum.photos/seed/inception/300/450'
  },
  {
    id: 2, title: '어바웃 타임', rating: 9.2, likes: 0, genre: '로맨스', isEditing: false,
    poster: 'https://picsum.photos/seed/abouttime/300/450'
  },
  {
    id: 3, title: '다크 나이트', rating: 9.8, likes: 0, genre: '액션', isEditing: false,
    poster: 'https://picsum.photos/seed/darkknight/300/450'
  },
  {
    id: 4, title: '기생충', rating: 8.9, likes: 0, genre: '드라마', isEditing: false,
    poster: 'https://picsum.photos/seed/parasite/300/450'
  },
  {
    id: 5, title: '라라랜드', rating: 6.8, likes: 0, genre: '로맨스', isEditing: false,
    poster: 'https://picsum.photos/seed/lalaland/300/450'
  },
  {
    id: 6, title: '인터스텔라', rating: 8.6, likes: 0, genre: 'SF', isEditing: false,
    poster: 'https://picsum.photos/seed/interstellar/300/450'
  }
]);

const sortOrder = ref(0);

//update 로직 (좋아요)
const handleLike = (targetId) => {
  const movie = movies.value.find(m => m.id === targetId);
  if (movie) movie.likes++;
  sortMovie();
};
//Delete 로직 (삭제)
const handleDelete = (targetId) => {
  movies.value = movies.value.filter(m => m.id !== targetId);
};
//Edit 로직 (수정)
const handleEdit = (targetId) => {
  const movie = movies.value.find(m => m.id === targetId);
  if (movie) {
    movie.isEditing = true;
  }
};
//Cancel 로직 (수정 취소)
const handleCancel = (targetId) => {
  const movie = movies.value.find(m => m.id === targetId);
  if (movie) {
    movie.isEditing = false;
  }
};
//Save 로직 (저장)
const handleSave = (targetId, updatedData) => {
  const movie = movies.value.find(m => m.id === targetId);
  if (movie) {
    movie.title = updatedData.title;
    movie.rating = updatedData.rating;
    movie.isEditing = false;
    sortMovie();
  }
};

//평점 순 정렬
const sortByRating = () => {
  if(sortOrder.value === 1)
    sortOrder.value = 0; //한번 더 클릭한 경우 취소
  else sortOrder.value = 1;
  sortMovie();
}

//좋아요(추천) 순 정렬
const sortByLikes = () => {
  if(sortOrder.value === 2)
    sortOrder.value = 0; //한번 더 클릭한 경우 취소
  else sortOrder.value = 2;
  sortMovie();
}

//영화 카드 정렬
const sortMovie = () => {
  if(sortOrder.value === 1){
    movies.value.sort((a, b) => b.rating - a.rating);
  }
  else if(sortOrder.value === 2){
    movies.value.sort((a, b) => b.likes - a.likes);
  }
  else{
    movies.value.sort((a, b) => a.id - b.id);
  }
}

</script>
<template>
  <div class="container">
    <h2>영화 리스트</h2>
    <template v-if="movies.length>0">
      <div class="header-list">
        <p class="total-count">총 {{ movies.length }}개</p>
        <div class="btn-group">
          <button class="btn sort-rating" :class="{'on': sortOrder===1}" @click="sortByRating">평점 높은 순</button>
          <button class="btn sort-likes" :class="{'on': sortOrder===2}" @click="sortByLikes">좋아요 많은 순</button>
        </div>
      </div>
      <main class="movie-grid">
        <!--자식이 쓴 이벤트를 여기서 수신 대기-->
        <MovieCard
          v-for="m in movies"
          :key="m.id"
          :movie="m"
          @like-movie="handleLike"
          @delete-movie="handleDelete"
          @edit-movie="handleEdit"
          @cancel-movie="handleCancel"
          @save-movie="handleSave"
        />
      </main>
    </template>
    <template v-else>
      <p class="no-movie">영화가 없습니다.</p>
    </template>
  </div>
</template>

<style scoped>
.container { 
  width: 100%;
  padding: 40px; 
  font-family: sans-serif; 
  max-width: 1000px; 
  margin: 0 auto; 
  background-color: #f8f9fa;
  min-height: 100vh;
}
h2 { 
  text-align: center; 
  margin-bottom: 40px; 
  color: #34495e;
  font-weight: 800;
}
.movie-grid { 
  display: grid; 
  grid-template-columns: repeat(auto-fill, minmax(240px, 1fr)); 
  gap: 25px; 
}

.header-list{
  display: flex;
  justify-content: space-between;
  align-items: center;
}
.no-movie{
  margin-top: 20%;
  text-align: center;
  color: gray;
}
.total-count{
  color: rgb(191, 191, 191);
  font-weight: bold;
}

.btn-group { 
  display: flex; 
  gap: 10px; 
  justify-content: right; 
  margin-bottom: 10px;
}
.btn {
  padding: 10px 15px; 
  cursor: pointer; 
  border: none; 
  border-radius: 6px; 
  font-weight: bold; 
  font-size: 0.9rem;
  transition: opacity 0.2s;
  background-color: #dcdcdc; 
}
.btn:hover { 
  opacity: 0.8; 
}
.sort-likes.on{
  background-color: #e74c3c; 
  color: white;
}
.sort-rating.on{
  background-color: #f39c12; 
  color: white; 
}

</style>