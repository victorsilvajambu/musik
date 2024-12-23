<script setup lang="ts">
import axios from 'axios';
import { onMounted, ref } from 'vue';
import { useRoute } from 'vue-router';

interface Track {
  name: string
  artist: {
    name: string
  }
  album?: {
    title: string
    image: {
      '#text': string
    }[]
  }
  duration: number
  playcount: number
  listeners: number
  toptags: {
    tag: {
      name: string
    }[]
  }
  wiki: {
    content: string
  }
}

const route = useRoute()

const apiKey = import.meta.env.VITE_API_KEY

const track = ref<Track>({
  name: '',
  artist: {
    name: ''
  },
  duration: 0,
  playcount: 0,
  listeners: 0,
  toptags: {
    tag: []
  },
  wiki: {
    content: ''
  }
})

async function getTrackInfo() {
  await axios.get('http://ws.audioscrobbler.com/2.0', {
    params: {
      format: 'json',
      api_key: apiKey,
      method: 'track.getinfo',
      track: route.params.track,
      artist: route.params.artist
    }
  }).then(response => {
    track.value = response.data.track
  })
}

function computeMinutes(duration: number) {
  const durationInSeconds = duration/1000
  const minutes = Math.floor(durationInSeconds/60)
  const seconds = durationInSeconds % 60
  return minutes + ':' + seconds
}

onMounted(() => getTrackInfo())

</script>

<template>
  <div class="flex w-full gap-12 p-12 text-white shrink-0">
    <div class="flex flex-col gap-8 shrink-0">
      <img
        v-if="track.album"
        :src="track.album.image[3]['#text']"
        class="rounded border-[1px] border-neutral-700"
      >

      <div class="flex gap-2">
        <UIcon
          name="i-fluent-play-circle-28-filled"
          class="size-6 text-red-400"
        />

        <div class="flex flex-col gap-2">
          <span class="text-xs font-medium">Reproduções</span>

          <span class="text-xs">{{ track.playcount }}</span>
        </div>
      </div>

      <div class="flex gap-2">
        <UIcon
          name="i-fluent-headphones-48-filled"
          class="size-6 text-red-400"
        />

        <div class="flex flex-col gap-2">
          <span class="text-xs font-medium">Ouvintes</span>

          <span class="text-xs">{{ track.listeners }}</span>
        </div>
      </div>
    </div>


    <div class="flex flex-col w-full gap-12">
      <span class="font-bold text-2xl">{{ track.name }}</span>

      <div class="flex gap-24 w-full">
        <div class="flex gap-2">
          <UIcon
            name="i-fluent-person-12-filled"
            class="size-6 text-neutral-600"
          />

          <div class="flex flex-col gap-2">
            <span class="font-medium">Artista</span>

            <span class="text-sm">{{ track.artist.name }}</span>
          </div>
        </div>

        <div
          v-if="track.album"
          class="flex gap-2"
        >
          <UIcon
            name="i-hugeicons-vynil-01"
            class="size-6 text-neutral-600"
          />

          <div class="flex flex-col gap-2">
            <span class="font-medium">Álbum</span>

            <span class="text-sm">{{ track.album.title }}</span>
          </div>
        </div>

        <div class="flex gap-2">
          <UIcon
            name="i-fluent-clock-12-filled"
            class="size-6 text-neutral-600"
          />

          <div class="flex flex-col gap-2">
            <span class="font-medium">Duração</span>

            <span class="align-middle text-sm">{{ computeMinutes(track.duration) }} min</span>
          </div>
        </div>
      </div>

      <div class="flex flex-col gap-2 w-full">
        <div class="flex gap-2">
          <UIcon
            name="i-fluent-tag-28-filled"
            class="size-6"
          />

          <span class="font-medium">Tags</span>
        </div>

        <div class="flex gap-2 flex-wrap">
          <UBadge
            v-for="tag in track.toptags.tag"
            :key="tag.name"
            color="error"
            variant="outline"
            size="lg"
          >
            {{ tag.name }}
          </UBadge>
        </div>
      </div>

      <span v-html="track.wiki.content" />
    </div>
  </div>
</template>
