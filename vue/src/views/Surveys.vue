<!-- This example requires Tailwind CSS v2.0+ -->
<template>
  <PageComponent>
    <template v-slot:header>
      <div class="flex justify-between">
        <h1 class="text-3xl font-bold text-gray-900">Surveys</h1>
        <router-link :to="{ name: 'SurveyCreate' }" class="py-2 px-3 text-white bg-emerald-500 rounded-md hover:bg-emerald-600">
          <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4 -mt-1 inline-block" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
            <path stroke-linecap="round" stroke-linejoin="round" d="M12 4v16m8-8H4" />
          </svg>
          Add New Survey
        </router-link>
      </div>
    </template>

    <div v-if="surveys.loading" class="flex justify-center">Loading...</div>
    <div v-else>
      <div class="grid grid-cols-1 gap-3 sm:grid-cols-2 md:grid-cols-3">
        <SurveyListItem v-for="(survey, ind) in surveys.data" 
                        :key="survey.id" :survey="survey"
                        @delete="deleteSurvey(survey)"
                        class="animate-fade-in-down"
                        :style="{animationDelay: `${ind * 0.1}s`}">
        </SurveyListItem>
      </div>
      <div class="flex jsutify-center mt-5">
        <nav class="relative z-0 inline-flex justify-center rounded-md shadow-sm" aria-label="Pagination">
          <a v-for="(link, i) of surveys.links" :key="i" :disabled="!link.url" v-html="link.label"
             href="#" @click="getForPage($event, link)" aria-current="page" class="relative inline-flex items-center px-4 py-2 border text-sm font-medium whitespace-nowrap"
             :class="[
                link.active
                  ? 'z-10 bg-indigo-50 border-indigo-500 text-indigo-600'
                  : 'bg-white border-gray-300 text-gray-500 hover:bg-gray-50',
                i === 0 ? 'rounded-l-md bg-gray-100 text-gray-700' : '',
                i === surveys.links.length - 1 ? 'rounded-r-md' : '',
             ]">

          </a>
        </nav>
      </div>
    </div>
  </PageComponent>
</template>

<script setup>
import store from '../store';
import { computed } from "vue"
import PageComponent from "../components/PageComponent.vue";
import SurveyListItem from "../components/SurveyListItem.vue";

const surveys = computed(() => store.state.surveys);

store.dispatch('getSurveys');

function deleteSurvey(survey) {
  if (confirm(`Are you sure you want to delete this survey? Oeration can't be undone!!!`)) {
    store.dispatch('deleteSurvey', survey.id).then(() => {
      store.dispatch('getSurveys');
    });
  }
}

function getForPage($event, link) {
  $event.preventDefault();
  if (!link.url || link.active) return;

  store.dispatch("getSurveys", { url: link.url });
}

</script>