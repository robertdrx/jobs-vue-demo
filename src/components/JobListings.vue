<script setup>
import { RouterLink } from 'vue-router';
import { ref,reactive, defineProps, onMounted } from 'vue';
import JobListing from '@/components/JobListing.vue';
import axios from 'axios';
import PulseLoader from 'vue-spinner/src/PulseLoader.vue';

// const jobs = ref([]);

const state = reactive({
    jobs:[],
    isLoading: true
});

defineProps({
    limit: Number,
    showButton : {
        type: Boolean,
        default: false
    }
});

function sleep(ms) {
  return new Promise(resolve => setTimeout(resolve, ms));
}

onMounted(async () => {
    try {
        const response = await axios.get('/api/jobs');
        state.jobs = response.data;
    } catch (error) {
        console.error('Error fetching jobs', error);
    } finally {
        await sleep(500);
        state.isLoading = false;
    }
});
</script>

<template>
    <section class="bg-blue-50 px-4 py-10">
        <div class="container-xl lg:container m-auto">
            <h2 class="text-3xl font-bold text-green-500 mb-6 text-center">
                Browse Jobs
            </h2>
            <!-- Show loading spinner -->
            <div v-if="state.isLoading" class="text-center text-gray-500 py-6">
                <PulseLoader />
            </div>

            <!-- Show job listings when loading is done -->
            <div v-else class="grid grid-cols-1 md:grid-cols-3 gap-6">
                <JobListing v-for="job in state.jobs.slice(0, limit || state.jobs.length)" :key="job.id" :job="job" />
            </div>
        </div>
    </section>

    <section v-if="showButton" class="m-auto max-w-lg my-10 px-6">
      <RouterLink
        to="/jobs"
        class="block bg-black text-white text-center py-4 px-6 rounded-xl hover:bg-gray-700"
        >View All Jobs</RouterLink>
    </section>

</template>