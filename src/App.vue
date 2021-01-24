<template>
  <div id="app">
    <h1>
      <font-awesome-icon icon="film" /> List of available movies
      <font-awesome-icon icon="film" />
    </h1>
    <div class="input">
      <p>Filter <Input :categories="categories" @CHANGE="catChange" /></p>
    </div>
    <div class="film" v-if="!filterToggle && show">
      <Card
        v-for="item in movies.slice(indexElement[0], indexElement[1])"
        @THUMB="thumb"
        @DELETE="deleteCard"
        :key="item.id"
        :id="item.id"
        :title="item.title"
        :like="item.likes"
        :dislike="item.dislikes"
        :userLike="item.userLike"
        :userDislike="item.userDislike"
        :category="item.category"
      />
    </div>
    <div class="filterBox" v-else-if="show">
      <Card
        v-for="item in filter.slice(indexElement[0], indexElement[1])"
        @THUMB="thumb"
        @DELETE="deleteCard"
        :key="item.id"
        :id="item.id"
        :title="item.title"
        :like="item.likes"
        :dislike="item.dislikes"
        :userLike="item.userLike"
        :userDislike="item.userDislike"
        :category="item.category"
      />
    </div>
    <div class="pageSelect">
      <div class="nav" @click="nav('previous')">
        <font-awesome-icon class="chevron" :icon="['fas', 'chevron-left']" />
        <p>Previous</p>
      </div>
      <p
        class="numbCard"
        :class="{ selected: nbElementDisplay == 4 }"
        @click="changeDisplay(4)"
      >
        4
      </p>
      <p
        class="numbCard"
        :class="{ selected: nbElementDisplay == 8 }"
        @click="changeDisplay(8)"
      >
        8
      </p>
      <p
        class="numbCard"
        :class="{ selected: nbElementDisplay == 12 }"
        @click="changeDisplay(12)"
      >
        12
      </p>

      <div class="nav two" @click="nav('next')">
        <p>Next</p>
        <font-awesome-icon class="chevron" :icon="['fas', 'chevron-right']" />
      </div>
    </div>
  </div>
</template>

<script>
import Input from "./components/Input";
import data from "./data.js";
import Card from "./components/Card.vue";
export default {
  name: "App",
  components: {
    Card,
    Input,
  },
  data() {
    return {
      movies: [],
      categories: [],
      filter: [],
      filterToggle: false,
      nbElementDisplay: 4,
      nbPages: Number,
      indexPage: 0,
      indexElement: [0, 4],
      show: false,
    };
  },
  methods: {
    thumb(payload) {
      let index = 0;
      while (this.movies[index].id != parseInt(payload.id)) {
        index++;
      }
      if (payload.event == "up") {
        if (payload.first) {
          this.movies[index].likes++;
          this.movies[index].userLike = true;
        } else {
          this.movies[index].likes++;
          this.movies[index].userLike = true;
          this.movies[index].dislikes--;
          this.movies[index].userDislike = false;
        }
      } else {
        if (payload.first) {
          this.movies[index].dislikes++;
          this.movies[index].userDislike = true;
        } else {
          this.movies[index].dislikes++;
          this.movies[index].userDislike = true;
          this.movies[index].likes--;
          this.movies[index].userLike = false;
        }
      }
    },
    deleteCard(payload) {
      if (this.filterToggle) {
        let indexFilter = 0;
        while (this.filter[indexFilter].id != parseInt(payload.id)) {
          indexFilter++;
        }
        this.filter.splice(indexFilter, 1);
        if (this.filter.length == 0) {
          document.getElementById("categories").value = "All";
          this.catChange({ cat: "All" });
        }
      }
      let index = 0;
      while (this.movies[index].id != parseInt(payload.id)) {
        index++;
      }
      this.movies.splice(index, 1);
      this.categories = [];
      this.movies.forEach((element) => {
        if (!this.categories.includes(element.category)) {
          this.categories.push(element.category);
        }
      });
    },
    catChange(payload) {
      this.indexElement = [0, 4];
      this.indexPage = 0;
      if (payload.cat != "All") {
        this.filter = [];
        this.movies.forEach((element) => {
          if (element.category == payload.cat) {
            this.filter.push(element);
          }
        });
        this.filterToggle = true;
        this.nbPages = Math.floor(this.filter.length / this.nbElementDisplay);
        if (this.filter.length % this.nbElementDisplay != 0) {
          this.nbPages++;
        }
        if (this.filter.length < this.nbElementDisplay) {
          this.indexElement[1] = this.filter.length;
        }
      } else {
        this.filterToggle = false;
        this.nbPages = Math.floor(this.movies.length / this.nbElementDisplay);
        if (this.movies.length % this.nbElementDisplay != 0) {
          this.nbPages++;
        }
      }
    },
    changeDisplay(number) {
      this.nbElementDisplay = number;
      if (!this.filterToggle) {
        this.nbPages = Math.floor(this.movies.length / this.nbElementDisplay);
        if (this.movies.length % this.nbElementDisplay != 0) {
          this.nbPages++;
        }
      } else {
        this.nbPages = Math.floor(this.filter.length / this.nbElementDisplay);
        if (this.filter.length % this.nbElementDisplay != 0) {
          this.nbPages++;
        }
      }
      if (this.movies.length < this.nbElementDisplay && !this.filterToggle) {
        this.indexElement = [0, this.movies.length];
      } else if (
        this.filter.length < this.nbElementDisplay &&
        this.filterToggle
      ) {
        this.indexElement = [0, this.filter.length];
      } else {
        this.indexElement = [0, this.nbElementDisplay];
      }
      this.indexPage = 0;
      this.show = false;
      this.show = true;
    },
    nav(way) {
      if (way == "next") {
        if (
          (this.indexElement[1] != this.movies.length && !this.filterToggle) ||
          (this.indexElement[1] != this.filter.length && this.filterToggle)
        ) {
          this.indexPage++;
          if (this.indexPage == this.nbPages - 1) {
            this.indexElement[0] += this.nbElementDisplay;
            if (this.filterToggle) {
              this.indexElement[1] = this.filter.length;
            } else {
              this.indexElement[1] = this.movies.length;
            }
          } else {
            this.indexElement[0] += this.nbElementDisplay;
            this.indexElement[1] += this.nbElementDisplay;
          }
        }
      } else {
        if (this.indexElement[0] != 0) {
          if (this.indexPage == this.nbPages - 1) {
            this.indexElement[0] -= this.nbElementDisplay;
            this.indexElement[1] = this.indexElement[0] + this.nbElementDisplay;
          } else {
            this.indexElement[0] -= this.nbElementDisplay;
            this.indexElement[1] -= this.nbElementDisplay;
          }
        }
        if (this.indexPage > 0) {
          this.indexPage--;
        }
      }
      this.show = false;
      this.show = true;
    },
  },
  mounted: function() {
    data
      .then((res) => {
        this.movies = res;
        this.show = true;
      })
      .finally(() => {
        this.movies.forEach((element) => {
          if (!this.categories.includes(element.category)) {
            this.categories.push(element.category);
          }
        });
        this.nbPages = Math.floor(this.movies.length / this.nbElementDisplay);
        if (this.movies.length % this.nbElementDisplay != 0) {
          this.nbPages++;
        }
      });
  },
};
</script>

<style>
:root {
  font-family: "Roboto", sans-serif;
}
h1 {
  letter-spacing: 2px;
  margin-top: 20px;
  text-align: center;
}
#app {
  position: absolute;
  top: 0;
  bottom: 0;
  left: 0;
  right: 0;
  display: flex;
  flex-direction: column;
  align-items: center;
}
.film,
.filterBox {
  display: flex;
  flex: 1;
  flex-wrap: wrap;
  justify-content: space-around;
  align-items: center;
  width: 100%;
}
.input p {
  display: flex;
  align-items: center;
  justify-content: space-between;
  font-size: 25px;
  width: 270px;
}
::-webkit-scrollbar-thumb {
  width: 5px;
  background: grey;
  border-radius: 100px;
}
::-webkit-scrollbar {
  width: 5px;
}
.pageSelect {
  width: 70%;
  display: flex;
  justify-content: center;
  align-items: center;
}
.nav {
  display: flex;
  align-items: center;
  justify-content: space-around;
  transition: all ease 200ms;
  user-select: none;
  width: 120px;
  padding: 0px 10px;
  margin: 20px 20px;
  font-size: 20px;
  cursor: pointer;
}

.nav:hover,
.numbCard:hover,
.selected {
  border: none;
  border-radius: 8px;
  background: #e0e0e0;
}
.chevron {
  transition: all ease 200ms;
}
.nav:hover:nth-child(1) > .chevron {
  transform: translate(-10px);
}
.two:hover > .chevron {
  transform: translate(10px);
}

.numbCard {
  margin: 0 5px;
  font-size: 20px;
  cursor: pointer;
  padding: 10px 15px;
  transition: all ease 200ms;
}
</style>
