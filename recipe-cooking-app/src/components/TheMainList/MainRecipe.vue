<template>
  <div class="basis-[70%] bg-red-200 rounded-br-lg">
    <div class="pt-10" v-show="isLoading">
      <base-spiner></base-spiner>
    </div>
    <!-- begin div no data -->
    <div v-show="!isLoading">
      <div
        v-if="recipe.length === 0"
        class="w-1/2 mx-auto text-lg font-semibold pt-20"
      >
        <div class="flex items-center gap-2">
          <svg
            xmlns="http://www.w3.org/2000/svg"
            fill="none"
            viewBox="0 0 24 24"
            stroke-width="2"
            stroke="#7f1d1d"
            class="w-8 h-8"
          >
            <path
              stroke-linecap="round"
              stroke-linejoin="round"
              d="M15.182 15.182a4.5 4.5 0 01-6.364 0M21 12a9 9 0 11-18 0 9 9 0 0118 0zM9.75 9.75c0 .414-.168.75-.375.75S9 10.164 9 9.75 9.168 9 9.375 9s.375.336.375.75zm-.375 0h.008v.015h-.008V9.75zm5.625 0c0 .414-.168.75-.375.75s-.375-.336-.375-.75.168-.75.375-.75.375.336.375.75zm-.375 0h.008v.015h-.008V9.75z"
            />
          </svg>

          <h1 class="flex">Start by choosing a recipe. Have fun!</h1>
        </div>
      </div>
      <!-- end div no data -->

      <!-- begin div have data -->
      <div class="w-full relative" v-else>
        <figure>
          <img
            class="w-full opacity-50 h-[380px] bg-gradient-to-br from-red-50 to-red-400"
            :src="recipe.image"
            :alt="recipe.title"
          />
        </figure>

        <div
          class="bg-gradient-to-br from-red-700 to-red-950 rounded-lg text-sm font-semibold uppercase text-gray-100 -skew-x-12 origin-bottom -rotate-6 p-4 absolute left-[50%] -translate-x-[50%] top-[330px]"
        >
          <p>{{ recipe.title }}</p>
        </div>

        <!-- line space -->
        <ControlMainRecipe />

        <!-- line space -->
        <IngredientsMainRecipe :recipe="recipe" />

        <!-- line space -->
        <FooterMainRecipe :recipe="recipe" />
      </div>
      <!-- end div have data -->
    </div>
  </div>
</template>

<script setup>
import ControlMainRecipe from "./MainRecipeList/ControlMainRecipe.vue";
import IngredientsMainRecipe from "./MainRecipeList/IngredientsMainRecipe.vue";
import FooterMainRecipe from "./MainRecipeList/FooterMainRecipe.vue";

import { watch, ref, computed } from "vue";
// store vuex
import { useStore } from "vuex";
const store = useStore();

//Spiner Loading
const isLoading = ref(false);

// data recipe
const recipe = computed(() => {
  return store.getters["Recipe/isRecipe"];
});

watch(
  () => store.getters["Recipe/changeId"],
  async () => {
    isLoading.value = true;
    await store.dispatch("Recipe/fetchRecipe");
    isLoading.value = false;
  }
);

const changeId = ref("");

// Nhận Id từ props route
const propId = defineProps(["rid"]);

// Theo dõi prodId và cập nhật Id mới
watch(
  () => propId.rid,
  (id) => {
    changeId.value = id;
  }
);
// Nếu có sự thay đổi id thì Update và chuyển id về state
watch(
  () => changeId.value,
  function takeId() {
    store.commit({
      type: "Recipe/takeId",
      value: changeId.value,
    });
  }
);
</script>
