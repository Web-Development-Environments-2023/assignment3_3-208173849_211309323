<template>
  <b-container fluid>
    <b-row>
      <RecipePreviewList :recipesList="recipes" title="My recipes" />
    </b-row>
    <b-row>
      <h3 v-if="recipes.length <= 0" class="no-results">
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
      this.getRecipes();
    }
  },
  data() {
    return {
      recipes: [],
      isLoggedIn: !!this.$root.store.username,
    };
  },
  methods: {
    async getRecipes() {
      try {
        const response = await this.axios.get(
          this.$root.store.server_domain + "/users/showMyRecipes"
        );
        let recipesArr = response.data;
        this.recipes = recipesArr;
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