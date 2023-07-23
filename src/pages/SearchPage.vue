<template>
  <b-container fluid id="search">
    <b-row class="title">Search Recipe</b-row>
    <b-row>
      <b-input-group prepend="Search Query:" id="search-input">
        <b-form-input v-model="searchQuery"></b-form-input>
        <b-input-group-append>
          <b-button variant="danger"  id="searchB" v-on:click="search">Search</b-button>
        </b-input-group-append>
      </b-input-group>
      <b-button
        variant="danger"
        class="ml-2"
        v-on:click="reset"
        v-if="showResults"
        >Reset</b-button
      >
      <b-input-group class="ml-auto">
        <b-col v-for="filter in filters" :key="filter.name">
          <b-form-group :description="filter.name">
            <b-form-select
              v-model="selectedFilters[filter.name]"
              :options="filter.values"
            ></b-form-select>
          </b-form-group>
        </b-col>
        <b-col>
          <b-form-select
            v-model="sort"
            :options="sortOptions"
            @change="sortResults"
          ></b-form-select>
        </b-col>
        <b-col>
          <b-form-group description="Limit">
            <b-form-select
              v-model="limit"
              :options="[5, 10, 15]"
              text-field="limit"
            ></b-form-select>
          </b-form-group>
        </b-col>
      </b-input-group>
    </b-row>
    <div v-if="showResults">
      <b-row>
        <RecipePreviewList
          :recipesList="results"
          title="Results"
          :class="!results.length ? 'ml-0' : ''"
        />
      </b-row>
      <b-row>
        <h3 v-if="results.length <= 0" class="no-results">
          No results to show...
        </h3></b-row
      >
    </div>
  </b-container>
</template>

<script>
import RecipePreviewList from "../components/RecipePreviewList";
import filterData from "../assets/filterData.json";
export default {
  name: "SearchPage",
  components: {
    RecipePreviewList,
  },
  data() {
    return {
      showResults: false,
      searchQuery: "",
      results: [],
      sort: "",
      sortOptions: [
        {
          value: "",
          text: "Sort by",
        },
        {
          value: "readyInMinutes",
          text: "Time to make",
        },
        {
          value: "aggregateLikes",
          text: "Popularity",
        },
      ],
      selectedFilters: {},
      filters: filterData,
      limit: 5,
    };
  },
  methods: {
    async search() {
      try {
        const response = await this.axios.get(
          this.$root.store.server_domain + "/recipes/search",
          {
            query: this.searchQuery,
            number: this.limit,
            cuisine: this.selectedFilters["Cuisines"]?.toLowerCase(),
            diet: this.selectedFilters["Diets"]?.toLowerCase(),
            intolerances: this.selectedFilters["Intolerances"]?.toLowerCase(),
          }
        );
        this.showResults = true;
        const results = response.data;
        if (Array.isArray(results)) {
          this.results = results;
        }
      } catch (err) {
        console.log(err);
      }
    },
    sortResults() {
      if (!this.sort) return;
      let sortCrit = this.sort;
      console.log(
        "before sort=",
        this.results.map((r) => r[sortCrit])
      );
      this.results.sort((a, b) => {
        return b[sortCrit] - a[sortCrit];
      });
      console.log(
        "after sort=",
        this.results.map((r) => r[sortCrit])
      );
    },
    reset() {
      this.showResults = false;
      this.searchQuery = "";
      this.results = [];
      this.sort = "";
      this.selectedFilters = {};
      this.limit = 5;
    },
  },
  mounted() {
    console.log("search page mounted");
  },
};
</script>

<style scoped lang="scss">

#searchB
{
  color: #000;
  background-color: #c3e6cb;
  border-color: #000;
}
#search-input {
  width: 500px;
  .input-group-append,
  .input-group-prepend {
    display: block;
  }
}
.input-group-text {
    display: flex;
    align-items: center;
    padding: 0.375rem 0.75rem;
    margin-bottom: 0;
    font-size: 1rem;
    font-weight: 400;
    line-height: 1.5;
    color: #495057;
    text-align: center;
    white-space: nowrap;
    background-color: #c3e6cb;
    border: 1px solid #212529;
    border-radius: 0.25rem;
}
#form-control {
  display: block;
  width: 100%;
  height: calc(1.5em + 0.75rem + 2px);
  padding: 0.375rem 0.75rem;
  font-size: 1rem;
  font-weight: 400;
  line-height: 1.5;
  color: #495057;
  background-color: #fff;
  background-clip: padding-box;
  border: 1px solid #212529;
  border-radius: 0.25rem;
  transition: border-color 0.15s
}
.no-results {
  font-size: 24px;
  padding: 0 40px;
}
#search {
  .row {
    margin: auto;
    .btn {
      height: fit-content;
    }
  }
  .input-group {
    &.ml-auto {
      width: auto;
    }
  }
}
</style>
