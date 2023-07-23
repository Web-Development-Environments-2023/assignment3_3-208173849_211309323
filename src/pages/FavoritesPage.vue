<template>
  <b-container fluid>
    <b-row>
      <RecipePreviewList :recipesList="favorites" title="My favorite recipes" />
    </b-row>
    <b-row>
      <h3 v-if="favorites.length <= 0" class="no-results">
        No results to show...
      </h3></b-row
    >
  </b-container>
</template>

<script>
import RecipePreviewList from "../components/RecipePreviewList.vue";
export default {
  components: {
    RecipePreviewList,
  },
  created() {
    if (this.isLoggedIn) {
      this.getFavorites();
    }
  },
  data() {
    return {
      favorites: this.$root.store.favorites ?? [],
      isLoggedIn: !!this.$root.store.username,
    };
  },
  methods: {
    async getFavorites() {
      try {
        const response = await this.axios.get(
          this.$root.store.server_domain + "/users/favorites"
        );
        let favoritesArr = response.data;
        this.favorites = favoritesArr;
        this.$root.store.favorites = favoritesArr;
      } catch (err) {
        console.log(err);
      }
    },
  },
};
</script>


<style scoped>
.no-results {
  font-size: 24px;
  padding: 0 40px;
}
</style>