<template>
  <b-container fluid id="main">
    <b-row>
      <div class="col-6">
        <RecipePreviewList
          title="Explore these recipes"
          class="RandomRecipes center"
          showButton
          :recipesList="this.randomRecipes"
          :fetchRecipes="this.getRandomRecipes"
        />
      </div>
      <div class="col-6">
        <b-button
          variant="danger"
          v-if="!$root.store.username"
          class="loginBtn"
          :to="{ name: 'login' }"
        >
          You need to login to vue these</b-button
        >
        <RecipePreviewList
          title="Last Viewed Recipes"
          :class="{
            RandomRecipes: true,
            blur: !$root.store.username,
            center: true,
          }"
          disabled
          :recipesList="lastWatchedRecipes"
        ></RecipePreviewList></div
    ></b-row>
  </b-container>
</template>

<script>
import RecipePreviewList from "../components/RecipePreviewList";
export default {
  components: {
    RecipePreviewList,
  },
  data() {
    return {
      lastWatchedRecipes: [],
      randomRecipes: [
        {
          id: 655235,
          title: "Peanut Butter and Jelly Smoothie",
          readyInMinutes: 45,
          image: "https://spoonacular.com/recipeImages/655235-556x370.jpg",
          aggregateLikes: 58,
          vegan: false,
          vegetarian: false,
          glutenFree: true,
        },
        {
          id: 634986,
          title: "Bing's Banana Cake",
          readyInMinutes: 45,
          image: "https://spoonacular.com/recipeImages/634986-556x370.jpg",
          aggregateLikes: 6,
          vegan: false,
          vegetarian: true,
          glutenFree: false,
        },
        {
          id: 658357,
          title: "Rigatoni With Sweet Sausages In Creamy Tomato Sauce",
          readyInMinutes: 45,
          image: "https://spoonacular.com/recipeImages/658357-556x370.jpg",
          aggregateLikes: 82,
          vegan: false,
          vegetarian: false,
          glutenFree: false,
        },
      ],
      isLoggedIn: !!this.$root.store.username,
    };
  },
  mounted() {
    // this.getRandomRecipes();
    if (this.isLoggedIn) this.getLastWatchedRecipes();
  },
  methods: {
    async getRandomRecipes() {
      try {
        const response = await this.axios.get(
          this.$root.store.server_domain + "/recipes/random"
          // "https://test-for-3-2.herokuapp.com/recipes/random"
        );
        const recipes = response.data;
        this.randomRecipes = [];
        this.randomRecipes.push(...recipes);
      } catch (error) {
        console.log(error);
      }
    },
    async getLastWatchedRecipes() {
      try {
        const res = await this.axios.get(
          this.$root.store.server_domain + "/users/watched"
        );
        const recipes = res.data;
        this.lastWatchedRecipes = recipes;
      } catch (err) {
        console.log(err);
      }
    },
  },
};
</script>

<style lang="scss" scoped>
#main {
  padding: 30px;
}
.RandomRecipes {
  margin: 10px 0 10px;
}
.blur {
  -webkit-filter: blur(5px); /* Safari 6.0 - 9.0 */
  filter: blur(2px);
}
::v-deep .blur .recipe-preview {
  pointer-events: none;
  cursor: default;
}

.loginBtn {
  z-index: 1;
  position: absolute;
  left: 60px;
  top: 60px;
  color: #000;
    background-color: #c3e6cb;
    border-color: #000;
}
</style>
