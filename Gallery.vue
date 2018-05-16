<template>
  <div class="gallery">

    <div class="gallery__photo"
      v-for="photo in photos"
      :key="photo.id">
      <img :src="photo.thumb"
        :alt="photo.name || ''"
        @click="selectPhoto(photo.id)">
    </div>

    <transition name="fade">
      <div class="gallery__modal"
        v-if="selectedPhoto">
        <img
          class="gallery__preview"
          :src="selectedPhoto.big"
          :alt="selectedPhoto.name || ''">

        <button type="button"
          class="close"
          @click="closePhoto" />
        <button type="button"
          class="arrow arrow--prev"
          @click="prev"> <
        </button>
        <button type="button"
          class="arrow arrow--next"
          @click="next"> >
        </button>

        <div class="gallery__thumbnails">
          <div class="gallery__photo"
            :class="{'selected': photo.id === selectedPhoto.id}"
            v-for="photo in photos"
            :key="photo.id">
            <img :src="photo.thumb"
              :alt="photo.name || ''"
              @click="selectPhoto(photo.id)">
          </div>
        </div>

      </div>
    </transition>

  </div>
</template>

<script>
import lodash from 'lodash'

export default {
  data() {
    return {
      photos: [
        {
          id: 1,
          name: 'Kitten 1',
          thumb: 'https://upload.wikimedia.org/wikipedia/commons/thumb/0/06/Kitten_in_Rizal_Park%2C_Manila.jpg/800px-Kitten_in_Rizal_Park%2C_Manila.jpg',
          big: 'https://upload.wikimedia.org/wikipedia/commons/thumb/0/06/Kitten_in_Rizal_Park%2C_Manila.jpg/800px-Kitten_in_Rizal_Park%2C_Manila.jpg'
        },
        {
          id: 2,
          name: 'Kitten 2',
          thumb: 'https://upload.wikimedia.org/wikipedia/commons/thumb/4/4d/SPCA_kITTEN.jpg/1280px-SPCA_kITTEN.jpg',
          big: 'https://upload.wikimedia.org/wikipedia/commons/thumb/4/4d/SPCA_kITTEN.jpg/1280px-SPCA_kITTEN.jpg'
        },
        {
          id: 3,
          name: 'Kitten 3',
          thumb: 'https://fthmb.tqn.com/4j55UCCc_TyTHtgwflSG8TeGpBU=/960x0/filters:no_upscale():max_bytes(150000):strip_icc()/kitten-looking-at-camera-521981437-57d840213df78c583374be3b.jpg',
          big: 'https://fthmb.tqn.com/4j55UCCc_TyTHtgwflSG8TeGpBU=/960x0/filters:no_upscale():max_bytes(150000):strip_icc()/kitten-looking-at-camera-521981437-57d840213df78c583374be3b.jpg'
        },
        {
          id: 4,
          name: 'Kitten 4',
          thumb: 'https://i.pinimg.com/originals/7a/f6/66/7af666b4ac7e96bd262ec27e5e7461a4.jpg',
          big: 'https://i.pinimg.com/originals/7a/f6/66/7af666b4ac7e96bd262ec27e5e7461a4.jpg'
        },
        {
          id: 5,
          name: 'Kitten 5',
          thumb: 'https://d2kwjcq8j5htsz.cloudfront.net/2015/09/04194922/kitten-walking-150904.jpg',
          big: 'https://d2kwjcq8j5htsz.cloudfront.net/2015/09/04194922/kitten-walking-150904.jpg'
        }
      ],
      selectedPhoto: null
    }
  },
  methods: {
    selectPhoto(photoId) {
      this.selectedPhoto = lodash.find(this.photos, { id: photoId })
    },
    closePhoto() {
      this.selectedPhoto = null
    },
    next() {
      let index = this.selectedIndex
      if (index < this.photos.length - 1) this.selectedPhoto = this.photos[index + 1]
      else this.selectedPhoto = this.photos[0]
    },
    prev() {
      let index = this.selectedIndex
      if (index > 0) this.selectedPhoto = this.photos[index - 1]
      else this.selectedPhoto = this.photos[this.photos.length - 1]
    }
  },
  computed: {
    selectedIndex() {
      return lodash.findIndex(this.photos, { id: this.selectedPhoto.id })
    }
  },
  created() {
    document.addEventListener('keydown', (event) => {
      if (event.key &&
        (event.key === 'Escape' || event.key === 'Esc') ||
        event.keyCode === 27
      ) this.closePhoto()
      if (event.key && event.key === 'ArrowLeft' || event.keyCode === 37) this.prev()
      if (event.key && event.key === 'ArrowRight' || event.keyCode === 39) this.next()
    })
  }
}
</script>

<style lang="scss">
/* transition */
.fade-enter-active, .fade-leave-active {
  transition: 0.2s;

  img {
    transition: 0.2s;
  }
}

.fade-enter, .fade-leave-to {
  opacity: 0;
}

/* gallery itself */
.gallery {
  text-align: center;

  &__photo {
    width: 200px;
    display: inline-block;
    margin: 10px;
    cursor: pointer;
    opacity: 0.9;
    transition: 0.2s;
    border: 1px transparent solid;

    &:hover,
    &.selected {
      opacity: 1;
    }

    img {
      width: 100%;
      max-width: 100%;
      max-height: 100%;
    }
  }

  &__thumbnails {
    width: 100%;
    position: absolute;
    left: 0;
    bottom: 50px;

    .gallery__photo {
      height: 80px;
      width: auto;

      &.selected {
        border-color: #555555;
      }

      img {
        width: auto;
      }
    }
  }

  &__modal {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    padding: 100px 100px 200px 100px;
    background: rgba(255, 255, 255, 0.9);

    .close {
      position: absolute;
      top: 10px;
      right: 10px;
    }
  }

  &__preview {
    max-width: 80%;
    max-height: 80%;
    position: absolute;
    left: 50%;
    top: calc(50% - 75px);
    transform: translate(-50%, -50%);
  }
}

.close {
  width: 30px;
  height: 30px;
  background: none;
  border: none;
  border-radius: 50%;

  &:after {
    content: "x"
  }
}

.arrow {
  position: absolute;
  top: calc(50% - 50px);
  transform: translateY(-50%);
  background: #444444;
  border: none;
  color: #ffffff;
  width: 30px;
  height: 30px;
  border-radius: 50%;

  &--prev {
    left: 20px;
  }

  &--next {
    right: 20px;
  }
}
</style>
