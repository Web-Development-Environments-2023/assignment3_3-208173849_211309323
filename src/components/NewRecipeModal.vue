<template>
  <b-modal
    id="new-recipe-modal"
    ref="addModal"
    title="Add a new recipe"
    :visible="isOpen"
    @show="resetModal"
    @hidden="resetModalAndClose"
    @ok="handleOk"
    no-close-on-backdrop
  >
    <form ref="addForm" @submit.stop.prevent="handleAddRecipe">
      <b-form-group
        label="Title"
        label-for="title-input"
        invalid-feedback="Title is required"
        :state="formValidState"
      >
        <b-form-input
          id="title-input"
          v-model="newRecipe.title"
          required
          :state="formValidState"
        ></b-form-input>
      </b-form-group>
      <b-form-group
        label="Image"
        label-for="image-input"
        invalid-feedback="Image is required"
        :state="formValidState"
      >
        <b-form-input
          id="image-input"
          v-model="newRecipe.image"
          :state="formValidState"
        ></b-form-input>
      </b-form-group>
      <b-input-group class="custom-group">
        <b-form-group
          label="Time to make (in minutes)"
          label-for="readyInMinutes-input"
          invalid-feedback="Time to make is required"
          :state="formValidState"
          class="w-45"
        >
          <b-form-input
            id="readyInMinutes-input"
            v-model="newRecipe.readyInMinutes"
            required
            :state="formValidState"
            type="number"
          ></b-form-input>
        </b-form-group>
        <b-form-group
          label="Number of servings"
          label-for="numOfServings-input"
          invalid-feedback="Number of servings is required"
          :state="formValidState"
          class="w-45"
        >
          <b-form-input
            id="numOfServings-input"
            v-model="newRecipe.numOfServings"
            required
            :state="formValidState"
            type="number"
          ></b-form-input> </b-form-group
      ></b-input-group>
      <b-form-group
        label="Ingredients"
        label-for="ingredients-input"
        description="Select amount and name of the ingredient"
      >
        <ul v-if="newRecipe.ingredients.length">
          <li
            v-for="ingredient in newRecipe.ingredients"
            :key="ingredient.name"
          >
            {{ ingredient.amount }} {{ ingredient.name }}
          </li>
        </ul>
        <b-input-group>
          <b-select
            id="ingredients-ammount-select"
            v-model="ingredientAmount"
            :options="[1, 2, 3, 4, 5, 6, 7, 8, 9, 10]"
            class="sm-select"
          ></b-select>
          <b-form-input
            id="ingredient-name-input"
            v-model="ingredientName"
          ></b-form-input>
          <b-input-group-append>
            <b-button @click="addIngredient"
              >Add</b-button
            ></b-input-group-append
          >
        </b-input-group>
      </b-form-group>
      <b-form-group
        label="Instructions"
        label-for="instructions-input"
        description="Add instructions by order"
      >
        <ol v-if="newRecipe.instructions.length">
          <li
            v-for="(instruction, index) of newRecipe.instructions"
            :key="index"
          >
            {{ instruction }}
          </li>
        </ol>
        <b-input-group>
          <b-form-input
            id="instructions-input"
            v-model="instructionText"
          ></b-form-input>
          <b-input-group-append>
            <b-button @click="addInstruction"
              >Add</b-button
            ></b-input-group-append
          >
        </b-input-group>
      </b-form-group>
      <b-form-group label="Additional Info">
        <b-input-group>
          <b-form-checkbox id="glutenFree" v-model="newRecipe.glutenFree"
            >Gluten Free</b-form-checkbox
          >
          <b-form-checkbox id="vegan" v-model="newRecipe.vegan"
            >Vegan</b-form-checkbox
          >
          <b-form-checkbox id="vegeterian" v-model="newRecipe.vegetarian"
            >Vegetarian</b-form-checkbox
          >
          <b-form-checkbox id="family" v-model="newRecipe.isFamily"
            >Family recipe</b-form-checkbox
          ></b-input-group
        >
      </b-form-group>
    </form></b-modal
  >
</template>

<script>
export default {
  name: "NewRecipeModal",
  props: {
    isOpen: {
      type: Boolean,
      required: true,
    },
  },
  data() {
    return {
      ingredientAmount: 0,
      ingredientName: "",
      instructionText: "",
      newRecipe: {
        title: "",
        image: "",
        readyInMinutes: "",
        ingredients: [],
        instructions: [],
        glutenFree: false,
        vegan: false,
        vegetarian: false,
        recipeOwner: this.$root.store.username,
        isFamily: false,
      },
      formValidState: null,
    };
  },
  mounted() {
    // if (this.isLoggedIn) this.fetchFavorites();
  },
  methods: {
    resetModal() {
      this.newRecipe = {
        title: "",
        image: "",
        readyInMinutes: "",
        ingredients: [],
        instructions: [],
        glutenFree: false,
        vegan: false,
        vegetarian: false,
        recipeOwner: this.$root.store.username,
        isFamily: false,
      };
      this.ingredientAmount = 0;
      this.ingredientName = "";
      this.instructionText = "";
      this.formValidState = null;
    },
    resetModalAndClose() {
      this.resetModal();
      this.$emit("close");
    },
    checkRecipeIsValid() {
      const valid = this.$refs.addForm.checkValidity();
      this.formValidState = valid;
      return valid;
    },
    handleOk(bvModalEvent) {
      bvModalEvent.preventDefault();
      this.handleAddRecipe();
    },
    async handleAddRecipe() {
      if (!this.checkRecipeIsValid()) {
        return;
      }
      try {
        const res = await this.axios.post(
          this.$root.store.server_domain +
            `/users/addRecipe`,
          {
            ...this.newRecipe,
          }
        );
        if (res.status === 200) {
          this.$root.toast(
            "New recipe added!",
            "Check it out on 'My recipes' page!",
            "success"
          );
          this.$nextTick(() => {
            this.$bvModal.hide("new-recipe-modal");
          });
          this.$router.go();
        }
      } catch (err) {
        console.log(err);
      }
    },
    addIngredient() {
      if (!this.ingredientAmount || !this.ingredientName) return;
      const newIngredient = {
        name: this.ingredientName,
        amount: this.ingredientAmount,
      };
      this.newRecipe.ingredients.push(newIngredient);
      this.ingredientName = "";
      this.ingredientAmount = 0;
    },
    addInstruction() {
      if (!this.instructionText) return;
      this.newRecipe.instructions.push(this.instructionText);
      this.instructionText = "";
    },
  },
};
</script>

<style>
</style>