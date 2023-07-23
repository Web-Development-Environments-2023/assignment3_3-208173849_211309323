<template>
  <div id="app">
    <div id="nav">
      <b-navbar toggleable="lg" type="light" id="mainbutton" variant="light">
        <b-navbar-brand :to="{ name: 'main' }" class="brand"
          >Grandma recipes</b-navbar-brand
        >

        <b-navbar-toggle target="nav-collapse"></b-navbar-toggle>

        <b-collapse id="nav-collapse" is-nav>
          <b-navbar-nav>
            <b-nav-item :to="{ name: 'main' }">Main</b-nav-item>
            <b-nav-item :to="{ name: 'search' }">Search</b-nav-item>
            <b-nav-item :to="{ name: 'about' }">About</b-nav-item>
            <b-nav-item-dropdown right v-if="isLoggedIn">
              <template #button-content> Personal </template>
              <b-dropdown-item :to="{ name: 'favoriteRecipes' }"
                >My favorites</b-dropdown-item
              >
              <b-dropdown-item :to="{ name: 'myRecipes' }"
                >My recipes</b-dropdown-item
              >
              <b-dropdown-item :to="{ name: 'familyRecipes' }"
                >My family recipes</b-dropdown-item
              >
            </b-nav-item-dropdown>
            <b-nav-item-dropdown right v-if="isLoggedIn">
              <template #button-content> new  </template>
            <b-dropdown-item @click="handleRecipeModal"
                  >Add new recipe</b-dropdown-item
                >
                <b-dropdown-item @click="handleFamliyRecipeModal"
                  >Add new famliy recipe</b-dropdown-item
                >
            </b-nav-item-dropdown>
                 
          </b-navbar-nav>
          <b-navbar-nav class="ml-auto">
            <b-nav-item-dropdown right>
              <template #button-content>
                <em>Hello {{ isLoggedIn ? $root.store.username : "Guest" }}</em>
              </template>
              <div v-if="$root.store.username">
                <b-dropdown-item id="loginregister" @click="Logout">Sign Out</b-dropdown-item>
              </div>
              
              <div v-else>
                <b-dropdown-item :to="{ name: 'register' }">
                  Register
                </b-dropdown-item>
                <b-dropdown-item :to="{ name: 'login' }">
                  Login
                </b-dropdown-item>
              </div>
            </b-nav-item-dropdown>
            
          </b-navbar-nav>
        </b-collapse>
      </b-navbar>
    </div>
    <NewRecipeModal :isOpen="showModal" @close="handleRecipeModal" />
    <FamilyRecipeModal :isOpen="showModal1" @close="handleFamliyRecipeModal" />
    <router-view />
  </div>
</template>

<script>
import NewRecipeModal from "./components/NewRecipeModal.vue";
import FamilyRecipeModal from "./components/FamliyRecipeModle.vue"
export default {
  name: "App",
  components: {
    NewRecipeModal,
    FamilyRecipeModal,
  },
  data() {
    return {
      isLoggedIn: !!this.$root.store.username,
      showModal: false,
      showModal1: false,
      favorites: [],
      watched: [],
    };
  },
  mounted() {
    // if (this.isLoggedIn) this.fetchFavorites();
  },
  methods: {
    Logout() {
      this.$root.store.logout();
      this.$root.toast("Logout", "User logged out successfully", "success");

      this.$router.go("/").catch(() => {
        this.$forceUpdate();
      });
    },
    async fetchFavorites() {
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
    handleRecipeModal() {
      this.showModal = !this.showModal;
    },
    handleFamliyRecipeModal() {
      this.showModal1 = !this.showModal1;
    },
  },
};
</script>

<style lang="scss">
@import "@/scss/form-style.scss";

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  min-height: 100vh;
}

#nav a {
  font-weight: bold;
  color: #0f0f0f;
}

#nav a.router-link-exact-active:not(.brand) {
  color: #ffffff;
}
#loginregister{
  background-color: #557b5e 
}

.container-fluid {
  padding: 30px;
  .row {
    &.title {
      font-size: 32px;
      color: black;
      margin: auto;
    }
  }
}

.sm-select {
  max-width: 15%;
}
.custom-checkbox {
  margin: 0 5px;
}

.custom-group {
  justify-content: space-between;

  .w-45 {
    width: 45% !important;
  }
}
#mainbutton
{
  background-color: #c3e6cb !important
}
</style>
