<script setup lang="ts">

import { onMounted, ref } from 'vue';
import axios from 'axios';

const apiKey = import.meta.env.VITE_API_KEY

const topTracks = ref([])

async function getTopTracks() {
  await axios.get('http://ws.audioscrobbler.com/2.0', {
    params: {
      format: 'json',
      api_key: apiKey,
      method: 'chart.gettoptracks'
    }
  }).then(response => {
    topTracks.value = response.data.tracks.track.slice(0, 5)
  })
}

onMounted(() => {
  getTopTracks()
})

</script>

<template>
  <div class="flex flex-col w-full p-12 items-center">
    <TrackList
      title="Músicas em Alta"
      :tracks="topTracks"
    />
  </div>
</template>
