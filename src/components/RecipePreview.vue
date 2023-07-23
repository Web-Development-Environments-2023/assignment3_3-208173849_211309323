<template>
  <div class="recipe-preview">
    <router-link
      :to="{ name: 'recipe', params: { recipeId: recipe.id } }"
      class="recipe-body"
      tag="div"
    > 
    <img :src="recipe.image" class="recipe-image"
    /></router-link>
    <div class="recipe-footer">
      <div :title="recipe.title" class="recipe-title">
        {{ recipe.title }}
      </div>
      <ul class="recipe-overview">
        <li>Time to make: {{ recipe.readyInMinutes }} minutes</li>
        <li>{{ recipe.aggregateLikes }} likes</li>
        <li v-if="recipe.glutenFree">Gluten free</li>
        <li v-if="recipe.vegan">Vegan</li>
        <li v-if="recipe.vegetarian">Vegeterian</li>
      </ul>
    </div>
    <div class="recipe-favorite" v-if="showFavoriteBtn" @click="addToFavorites">
      <b-button
        :variant="isRecipeFavorite ? 'danger' : 'outline-danger'"
        size="sm"
        >{{ isRecipeFavorite ? "In favorites" : "Add to favorites" }}</b-button
      >
    </div>
  </div>
</template>

<script>
export default {
  mounted() {
    // this.axios.get(this.recipe.image).then((i) => {
    //   this.image_load = true;
    // });
    this.isRecipeFavorite = this.isFavorite();
  },
  data() {
    return {
      // image_load: false,
      showFavoriteBtn: !!this.$root.store.username,
      favorites: this.$root.store.favorites ?? [],
      isRecipeFavorite: false,
    };
  },
  props: {
    recipe: {
      type: Object,
      required: true,
    },

    // id: {
    //   type: Number,
    //   required: true
    // },
    // title: {
    //   type: String,
    //   required: true
    // },
    // readyInMinutes: {
    //   type: Number,
    //   required: true
    // },
    // image: {
    //   type: String,
    //   required: true
    // },
    // aggregateLikes: {
    //   type: Number,
    //   required: false,
    //   default() {
    //     return undefined;
    //   }
    // }
  },
  methods: {
    async addToFavorites() {
      if (this.isFavorite(this.recipe.id)) return;
      try {
        const response = await this.axios.post(
          this.$root.store.server_domain + "/users/favorites",
          {
            recipeId: this.recipe.id,
          }
        );
        this.$root.toast(
          "Favorites",
          "Recipe was succesfully added to your favorites!",
          "danger"
        );
        this.isRecipeFavorite = true;
      } catch (err) {
        console.log(err);
      }
    },
    isFavorite() {
      if (this.favorites.length && this.recipe.id) {
        const favorite = this.favorites.find(({ id }) => id === this.recipe.id);
        if (favorite) return true;
      }
      return false;
    },
  },
};
</script>

<style scoped lang="scss">
.recipe-preview {
  display: flex;
  flex-direction: column;
  align-items: flex-start;
  min-height: 350px;
}

.recipe-preview .recipe-body .recipe-image {
  margin-left: auto;
  margin-right: auto;
  margin-top: auto;
  margin-bottom: auto;
  display: block;
  width: 100%;
  height: auto;
  -webkit-background-size: cover;
  -moz-background-size: cover;
  background-size: cover;
  cursor: pointer;
}

.recipe-preview .recipe-footer {
  width: 100%;
  height: auto;
  pointer-events: none;
  .recipe-title {
    margin-top: 10px;
    font-size: 16px;
    color: black;
    font-weight: 600;
  }
}
</style>
