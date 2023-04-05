<template>
  <div class="card-block">
    <div v-show="isModalOpen" class="painting-modal" @click="modalClose($event)">
      <div class="painting-modal_container" :style="{background: 'url(' + getBackgroundPath + ')' + ' center center / cover'}">
        <div class="painting-modal_content">
          <div class="painting-modal_close" @click="isModalOpen = !isModalOpen">X</div>
          <div class="row">
            <div class="col-6">
              <div class="painting-modal_slider">
                <div ref="swiper" class="swiper">
                  <div class="swiper-wrapper">
                    <div class="swiper-slide">
                      <img :src="getImageCardPath" :alt="title">
                    </div>
                    <div class="swiper-slide">
                      <img :src="getSliderPath" :alt="title">
                    </div>
                  </div>
                  <div class="swiper-pagination"></div>

                  <div class="swiper-button-prev"></div>
                  <div class="swiper-button-next"></div>

                  <div class="swiper-scrollbar"></div>
                </div>
              </div>
            </div>
            <div class="col-6">
              <div class="painting-modal_description">
                <h1>{{title}}</h1>
                <div class="painting-card_price">
                  <span v-if="isSold" class="painting-card_sold h3-style">Продана на аукционе</span>
                  <span v-if="oldPrice !== 'null'" class="painting-card_price--old h6-style crossed-out">{{ oldPrice }}</span>
                  <br v-if="oldPrice !== 'null'">
                  <span v-if="currentPrice !== 'null'" class="painting-card_price--current h3-style">{{ currentPrice }}</span>
                </div>
                <p class="h6-style">{{description}}</p>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
    <div class="painting-card" :class="{ sold: isSold}">
      <div class="loader" :class="{isShowed:isLoading}">
        <h6>Обрабатывается</h6>
        <div class="lds-ripple"><div></div><div></div></div>
      </div>
      <img class="painting-card_image" :src="getImageCardPath" :alt="title" @click="isModalOpen = !isModalOpen">
      <div class="painting-card_info">
        <h2>{{ title }}</h2>
        <div class="painting-card-pricing">
          <div class="painting-card_price">
            <span v-if="isSold" class="painting-card_sold h3-style">Продана на аукционе</span>
            <span v-if="oldPrice !== 'null'" class="painting-card_price--old h6-style crossed-out">{{ oldPrice }}</span>
            <br v-if="oldPrice !== 'null'">
            <span v-if="currentPrice !== 'null'" class="painting-card_price--current h3-style">{{ currentPrice }}</span>
          </div>

          <div class="button-block" v-if="!isSold">
            <span :class="{isClicked: isBucket}"></span>
            <button :class="{isBucket}" class="default-button h4-style" @click="toggleClass" ref="button">Купить</button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import Swiper, { Navigation, Pagination } from 'swiper'
import 'swiper/css'
import 'swiper/css/navigation'
import 'swiper/css/pagination'

export default {
  name: "card-component",
  props: {
    title: String,
    imageCardName: {
      type:String,
      default: '',
    },
    imageCardBackground: {
      type:String,
      default: '',
    },
    imageCardSlider: {
      type:String,
      default: '',
    },
    oldPrice: {
      type: String,
    },
    currentPrice: {
      type: String,
    },
    isSold: {
      type: Boolean,
    },
    isBucketState: {
      type: Boolean,
    },
    description: {
      type:String,
    },
    cardIndex: {
      type: Number,
    }
  },
  mounted() {
    this.isBucket = this.isBucketState;
    new Swiper(this.$refs.swiper, {
      modules: [Navigation, Pagination],
      loop: true,

      pagination: {
        el: '.swiper-pagination',
        clickable: true,
        type: 'custom',
        renderCustom: function (swiper, current, total) {
          let out = ''
          for (let i = 1; i < total+1; i++) {
            if (i === current) {
              out = out + '<span class="swiper-pagination-bullet swiper-pagination-bullet-active" tabindex='+i+' role="button" aria-label="Go to slide '+i+1+'"></span>';
            }
            else {
              out = out + '<span class="swiper-pagination-bullet" tabindex='+i+' role="button" aria-label="Go to slide '+i+1+'"></span>';
            }
          }
          return out;
        },
      },

      navigation: {
        nextEl: '.swiper-button-next',
        prevEl: '.swiper-button-prev',
      },

      scrollbar: {
        el: '.swiper-scrollbar',
      },
    })
  },
  computed: {
    getImageCardPath: function() {
      return this.getImagePath(this.imageCardName)
    },
    getBackgroundPath: function() {
      if (this.imageCardBackground) {
        return this.getImagePath(this.imageCardBackground)
      }
      return ''
    },
    getSliderPath: function() {
      if (this.imageCardSlider) {
        return this.getImagePath(this.imageCardSlider)
      }
      return ''
    },
    slideImages() {
      return [
        this.getImagePath(this.imageCardName),
        this.getImagePath(this.imageCardSlider)
      ];
    }
  },
  data() {
    return {
      isBucket: false,
      isLoading:false,
      isModalOpen: false,
    }
  },
  methods: {
    toggleClass() {
      if (this.isBucket) {
        this.isBucket = !this.isBucket
        this.changeButtonState()
      } else {
        this.isLoading = !this.isLoading
        new Promise(() => {
          setTimeout(() => {
            this.isBucket = !this.isBucket
            this.isLoading = !this.isLoading
            this.changeButtonState()
          }, 2000);
        })
      }
    },
    changeButtonState() {
      if (this.isBucket) {
        this.$refs.button.innerHTML = 'В корзине'
        this.$emit('cardStateChanged', this.isBucket, this.cardIndex)
      } else {
        this.$refs.button.innerHTML = 'Купить'
        this.$emit('cardStateChanged', this.isBucket, this.cardIndex)
      }
    },
    modalClose(event) {
      if (event.target?.firstChild?._prevClass === 'painting-modal_container') {
        this.isModalOpen = !this.isModalOpen
      }
    },
    getImagePath(imageName) {
      return require(`../assets/images/${imageName}`)
    }
  },
  watch: {
    isBucketState: function(newVal) {
      this.isBucket = newVal;
      this.changeButtonState()
    }
  }
}
</script>

<style>
.painting-card img {
  width: 100%;
}

.painting-card {
  margin-bottom: 10px;
  position: relative;
}

.painting-card_info {
  padding:20px 24px;
  border: 1px solid var(--border-color);
}

.painting-card-pricing {
  margin-top: 24px;
  justify-content: space-between;
  display: flex;
}

.painting-card_price {
  display: flex;
  flex-direction: column;
  justify-content: center;
}

.painting-card_image {
  cursor:pointer;
}

.painting-card_sold {
  margin-top: 10px;
}

.sold {
  opacity: 0.5;
}

.default-button.isBucket {
  padding: 15px 9px 15px 32px;
  background: var(--button-bucket-color);
}

.painting-modal {
  cursor: pointer;
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  z-index: 15;
  display: flex;
  background: rgba(0,0,0,.5);
  justify-content: center;
  align-items: center;
}

.painting-modal_close {
  position: absolute;
  bottom: 15px;
  left: 15px;
  color:#fff;
  font-size: 24px;
  cursor:pointer;
  font-weight: 700;
}

.painting-modal_content, .painting-modal_content .row {
  height: 100%;
}

.painting-modal_container {
  position: relative;
  cursor:default;
  border-radius: 15px;
  width: 920px;
  padding:15px;
  height: 500px;
}

.painting-modal_description {
  background: rgba(127, 90, 69, 0.77);
  color:#ECEAEA;
  padding: 10px;
  height:100%;
  border-radius: 15px;
}

.painting-modal_description p {
  margin-top: 10px;
}

.painting-modal .painting-card_price {
  margin-top: 20px;
}

/* loader */
.loader {
  opacity: 0;
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  z-index: -1;
  height: 100%;
  display: flex;
  color:#fff;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  background: rgba(0,0,0,.5);
  transition:all .2s linear;
}

.loader.isShowed {
  z-index: 10;
  opacity: 1;
}

.lds-ripple {
  display: inline-block;
  position: relative;
  width: 80px;
  height: 80px;
}
.lds-ripple div {
  position: absolute;
  border: 4px solid #fff;
  opacity: 1;
  border-radius: 50%;
  animation: lds-ripple 1s cubic-bezier(0, 0.2, 0.8, 1) infinite;
}
.lds-ripple div:nth-child(2) {
  animation-delay: -0.5s;
}
@keyframes lds-ripple {
  0% {
    top: 36px;
    left: 36px;
    width: 0;
    height: 0;
    opacity: 0;
  }
  4.9% {
    top: 36px;
    left: 36px;
    width: 0;
    height: 0;
    opacity: 0;
  }
  5% {
    top: 36px;
    left: 36px;
    width: 0;
    height: 0;
    opacity: 1;
  }
  100% {
    top: 0px;
    left: 0px;
    width: 72px;
    height: 72px;
    opacity: 0;
  }
}

/* SLIDER */


.swiper-slide {
  display: flex;
  justify-content: center;
  align-items: center;
  -webkit-box-shadow: 4px 4px 8px 0 rgba(34, 60, 80, 0.2);
  -moz-box-shadow: 4px 4px 8px 0 rgba(34, 60, 80, 0.2);
  box-shadow: 4px 4px 8px 0 rgba(34, 60, 80, 0.2);
}

.swiper-slide img {
  border-radius: 30px;
}

.swiper-button-next, .swiper-button-prev {
  color:#E1E1E1;
}
.swiper-pagination {
  position: absolute;
}
.swiper-pagination-bullet {
  margin: 0 5px !important;
}
.swiper-pagination-bullet-active {
  background-color: #E1E1E1 !important;
}
</style>