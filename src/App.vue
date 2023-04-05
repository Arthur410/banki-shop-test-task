<template>
  <div class="main">
    <div class="main-page">
      <header class="main-page_header">
        <div class="main-page_header-container">
          <div class="row">
            <div class="col-12 col-xxl-7  col-xl-7">
              <nav class="main-navigation">
                <ul>
                  <li><a href="#">Каталог</a></li>
                  <li><a href="#">Доставка</a></li>
                  <li><a href="#">Оплата</a></li>
                  <li><a href="#">Контакты</a></li>
                  <li><a href="#">О компании</a></li>
                </ul>
              </nav>
            </div>
            <div class="col-12 col-xxl-5  col-xl-5">
              <div class="main-search">
                <div class="main-search_input-block">
                  <input v-model="searchText" placeholder="Поиск по названию картины" type="search">
                  <button @click="filterCards" class="default-button h4-style">Найти</button>
                </div>
              </div>
            </div>
          </div>
        </div>
      </header>
      <main class="main-page_content">
        <div class="main-page_title">
          <h1>Картины эпохи Возрождения</h1>
        </div>
        <div class="main-page_painting-cards">
          <div class="row" ref="paintingCards">
            <div class="col-xxl-3 col-xl-6 col-lg-12 painting-card"
                 v-for="card in filteredCards"
                 :key="card.cardIndex"
            >
              <card-component
                  :key="card.cardIndex"
                  :card-index="parseInt(card.cardIndex)"
                  :title="card.title"
                  :old-price="card.oldPrice"
                  :current-price="card.price"
                  :image-card-name="card.mainImage"
                  :image-card-background="card.backgroundImage"
                  :image-card-slider="card.additionSliderImage"
                  :description="card.description"
                  :is-bucket-state="cardsInBucket.indexOf(parseInt(card.cardIndex)) !== -1"
                  @cardStateChanged="saveChangedCard"
                  :is-sold="card.sold === 'true'"
              />
            </div>
          </div>
        </div>
      </main>
      <footer class="main-page_footer">
        <div class="container">
          <div class="row">
            <div class="col-12 col-lg-6 col-xxl-7 col-xl-7">
              <nav class="footer_navigation">
                <ul>
                  <li><a href="#">Каталог</a></li>
                  <li><a href="#">Доставка</a></li>
                  <li><a href="#">Оплата</a></li>
                  <li><a href="#">Контакты</a></li>
                  <li><a href="#">О компании</a></li>
                </ul>
              </nav>
            </div>
            <div class="col-12 col-lg-6 col-xxl-5  col-xl-5">
              <div class="footer_information">
                <div class="phone">
                  <svg width="16" height="16" viewBox="0 0 16 16" fill="none" xmlns="http://www.w3.org/2000/svg">
                    <path d="M14.6861 12.0733L12.5241 9.9061C12.0934 9.47621 11.3803 9.48927 10.9346 9.9361L9.84534 11.0276C9.77652 10.9896 9.70529 10.9499 9.6304 10.9077C8.94254 10.5257 8.00109 10.0022 7.0104 9.00851C6.01678 8.01276 5.49391 7.06772 5.11161 6.37786C5.07127 6.30478 5.03261 6.23431 4.99445 6.16739L5.7255 5.43577L6.08492 5.07509C6.53125 4.62763 6.54356 3.91305 6.11392 3.48192L3.95184 1.31445C3.52219 0.883917 2.80871 0.896979 2.36238 1.34444L1.75303 1.95868L1.76968 1.97525C1.56536 2.23655 1.39462 2.53793 1.26756 2.86292C1.15044 3.17228 1.07752 3.46748 1.04418 3.76329C0.758688 6.13542 1.84023 8.30337 4.7754 11.2452C8.8327 15.3114 12.1023 15.0043 12.2434 14.9893C12.5506 14.9525 12.845 14.8789 13.1442 14.7624C13.4657 14.6366 13.7662 14.4657 14.0267 14.2613L14.04 14.2732L14.6573 13.6673C15.1028 13.22 15.1156 12.5051 14.6861 12.0733Z" fill="#555555"/>
                  </svg>
                  <span class="h5-style">+7 (812) 555-55-55</span>
                </div>
                <div class="location">
                  <svg width="16" height="16" viewBox="0 0 16 16" fill="none" xmlns="http://www.w3.org/2000/svg">
                    <path d="M8.07028 1C5.27451 1 3 3.27451 3 6.07025C3 9.53985 7.5374 14.6334 7.73058 14.8486C7.91204 15.0507 8.22884 15.0503 8.40997 14.8486C8.60315 14.6334 13.1406 9.53985 13.1406 6.07025C13.1405 3.27451 10.866 1 8.07028 1ZM8.07028 8.62123C6.66366 8.62123 5.51932 7.47687 5.51932 6.07025C5.51932 4.66363 6.66368 3.51929 8.07028 3.51929C9.47687 3.51929 10.6212 4.66366 10.6212 6.07028C10.6212 7.47689 9.47687 8.62123 8.07028 8.62123Z" fill="#555555"/>
                  </svg>
                  <span class="h5-style">г. Санкт-Петербург, ул. Ефимова, 3</span>
                </div>
              </div>
            </div>
          </div>
        </div>
      </footer>
    </div>
  </div>
</template>

<script>
import 'bootstrap/dist/css/bootstrap.css'
import CardComponent from './components/card.vue'
import dataCards from './json-info/cards.json'

export default {
  name: 'App',
  components: {
     CardComponent,
  },
  data() {
    return {
      cardsInBucket: [],
      searchText: '',
      cards: {
        card1: {
          cardIndex:1,
          title: dataCards.cards.card1.title,
          oldPrice: dataCards.cards.card1.oldPrice,
          price: dataCards.cards.card1.price,
          mainImage: dataCards.cards.card1.mainImage,
          backgroundImage: dataCards.cards.card1.backgroundImage,
          additionSliderImage: dataCards.cards.card1.additionSliderImage,
          description: dataCards.cards.card1.description,
          sold: dataCards.cards.card1.sold,
        },
        card2: {
          cardIndex:2,
          title: dataCards.cards.card2.title,
          oldPrice: dataCards.cards.card2.oldPrice,
          price: dataCards.cards.card2.price,
          mainImage: dataCards.cards.card2.mainImage,
          backgroundImage: dataCards.cards.card2.backgroundImage,
          additionSliderImage: dataCards.cards.card2.additionSliderImage,
          description: dataCards.cards.card2.description,
          sold: dataCards.cards.card2.sold,
        },
        card3: {
          cardIndex:3,
          title: dataCards.cards.card3.title,
          oldPrice: dataCards.cards.card3.oldPrice,
          price: dataCards.cards.card3.price,
          mainImage: dataCards.cards.card3.mainImage,
          backgroundImage: dataCards.cards.card3.backgroundImage,
          additionSliderImage: dataCards.cards.card3.additionSliderImage,
          description: dataCards.cards.card3.description,
          sold: dataCards.cards.card3.sold,
        },
        card4: {
          cardIndex:4,
          title: dataCards.cards.card4.title,
          oldPrice: dataCards.cards.card4.oldPrice,
          price: dataCards.cards.card4.price,
          mainImage: dataCards.cards.card4.mainImage,
          backgroundImage: dataCards.cards.card4.backgroundImage,
          additionSliderImage: dataCards.cards.card4.additionSliderImage,
          description: dataCards.cards.card4.description,
          sold: dataCards.cards.card4.sold,
        }
      },
      filteredCards: {}
    }
  },
  mounted() {
    let restoredCards = localStorage.getItem('cardsInBucket')
    if (restoredCards) {
      restoredCards = restoredCards.split(',')
          .map(item => parseInt(item))
          .sort()
      this.cardsInBucket = restoredCards
    }
    this.filteredCards = this.cards
  },
  methods: {
    saveChangedCard(state, value) {
      if (state) {
        if (this.cardsInBucket.indexOf(value) === -1) {
          this.cardsInBucket.push(value)
          this.cardsInBucket = this.cardsInBucket.sort()
        }
      } else {
        let index = this.cardsInBucket.indexOf(value);
        if (index > -1) {
          this.cardsInBucket.splice(index, 1);
        }
      }
      console.log(this.cardsInBucket)
      localStorage.setItem('cardsInBucket', this.cardsInBucket)
    },
    filterCards() {
      if (this.searchText) {
        const searchTerm = this.searchText.toLowerCase();
        this.filteredCards = Object.keys(this.cards)
            .map((key) => ({
              cardIndex: key.substr(-1),
              ...this.cards[key],
            }))
            .filter((card) =>
                card.title.toLowerCase().includes(searchTerm)
            );
      } else {
        this.filteredCards = this.cards
      }
    },
  }
}
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Merriweather:wght@300;400;700&display=swap');
@import './styles/reset.css';
@import './styles/style.css';
</style>
