<template>
  <div class="gallery">

    <div class="gallery__photo"
      v-for="photo in photos"
      v-if="showPreviews"
      :key="photo.id">
      <img :src="baseUrl + photo.path + '/' + photo.name"
        :alt="photo.originalName || ''"
        @click="selectPhoto(photo.id)">
    </div>

    <transition name="fade">
      <div class="gallery__modal" v-if="selectedPhoto">

        <div class="gallery__content" @click.stop="closePhoto">
          <img
            class="gallery__preview"
            :src="baseUrl + selectedPhoto.path + '/' + selectedPhoto.name"
            :alt="selectedPhoto.originalName || ''">

          <button type="button"
            class="close"
            @click="closePhoto" />

          <template v-if="showThumbnails">
            <button type="button"
              class="arrow arrow--prev"
              @click="prev"> &lt;
            </button>
            <button type="button"
              class="arrow arrow--next"
              @click="next"> &gt;
            </button>
          </template>
        </div>

        <div class="gallery__thumbnails" v-if="showThumbnails">
          <div class="gallery__photo"
            :class="{'selected': photo.id === selectedPhoto.id}"
            v-for="photo in photos"
            :key="photo.id">
            <img :src="baseUrl + photo.path + '/' + photo.name"
              :alt="photo.name || ''"
              @click="selectPhoto(photo.id)">
          </div>
        </div>

      </div>
    </transition>

  </div>
</template>

<script>
import { findIndex } from 'lodash'

export default {
  props: {
    photos: {
      type: Array
    },
    showThumbnails: {
      type: Boolean,
      default: true
    },
    showPreviews: {
      type: Boolean,
      default: true
    }
  },
  data () {
    return {
      selectedIndex: null,
      baseUrl: process.env.VUE_APP_UPLOADS_URL
    }
  },
  methods: {
    selectPhoto (photoId) {
      console.log(findIndex(this.photos, { id: photoId }))
      this.selectedIndex = findIndex(this.photos, { id: photoId })
    },
    closePhoto () {
      this.selectedIndex = null
    },
    next () {
      let index = this.selectedIndex
      if (index < this.photos.length - 1) this.selectedIndex = index++
      else this.selectedIndex = 0
    },
    prev () {
      let index = this.selectedIndex
      if (index > 0) this.selectedIndex = index--
      else this.selectedIndex = this.photos.length - 1
    },
    keydownListener (event) {
      if (event.key &&
        ((event.key === 'Escape' || event.key === 'Esc') ||
          event.keyCode === 27)
      ) this.closePhoto()
      if (event.key && (event.key === 'ArrowLeft' || event.keyCode === 37)) this.prev()
      if (event.key && (event.key === 'ArrowRight' || event.keyCode === 39)) this.next()
    }
  },
  computed: {
    selectedPhoto () {
      if (this.selectedIndex === null) return null
      return this.photos[this.selectedIndex]
    }
  },
  created () {
    document.addEventListener('keydown', this.keydownListener)
  },
  beforeDestroy () {
    document.removeEventListener('keydown', this.keydownListener)
  }
}
</script>
