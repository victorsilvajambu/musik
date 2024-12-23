<script setup lang="ts">
import axios from 'axios';
import { onMounted, ref } from 'vue';

interface Track {
  name: string
  artist: {
    name: string
  }
  album: {
    image: {
      '#text': string
    }[]
  }
}

const props = defineProps<{
  title: string
  tracks: Track[]
}>()

const apiKey = import.meta.env.VITE_API_KEY
const trackList = ref<Track[]>([])

async function getTracksInfo() {
  for(const track of props.tracks) {
    await axios.get('http://ws.audioscrobbler.com/2.0', {
      params: {
        format: 'json',
        api_key: apiKey,
        method: 'track.getinfo',
        track: track.name,
        artist: track.artist.name
      }
    }).then(response => {
      trackList.value.push(response.data.track)
    })
  }
}

onMounted(() => {
  setTimeout(() => {
    getTracksInfo()
  }, 500)
})

</script>

<template>
  <div class="flex flex-col gap-4">
    <div class="flex items-center gap-2 text-red-400">
      <UIcon
        name="i-fluent-ribbon-star-20-filled"
        class="size-6"
      />

      <span class="font-semibold">
        {{ title }}
      </span>
    </div>

    <div class="flex gap-4 flex-wrap">
      <div
        v-for="track in trackList"
        :key="track.name"
        class="flex md:flex-col w-full md:w-fit gap-4 shrink-0 bg-neutral-800 p-4 rounded-lg text-white"
      >
        <div v-if="track.album">
          <img
            :src="track.album.image[2]['#text']"
            class="rounded"
          >
        </div>

        <div
          v-else
          class="flex justify-center items-center bg-neutral-600 w-[174px] h-[174px] rounded"
        >
          <UIcon
            name="i-fluent-music-note-2-24-filled"
            class="size-16"
          />
        </div>

        <div class="flex flex-col">
          <span>
            {{ track.name }}
          </span>

          <span class="text-xs">
            {{ track.artist.name }}
          </span>
        </div>
      </div>
    </div>
  </div>
</template>
