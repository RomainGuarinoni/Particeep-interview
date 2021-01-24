<template>
  <div class="cardBox">
    <h2>{{ title }}</h2>
    <div class="categorie">
      <p>catégorie : {{ category }}</p>
    </div>
    <div class="thumbs">
      <div class="left">
        <p
          class="thumbBox"
          @click="thumb('up')"
          :class="{ likeToggle: likeToggle }"
        >
          <font-awesome-icon :icon="['fas', 'thumbs-up']" />
        </p>
        <p class="number">{{ like }}</p>
      </div>
      <div class="right">
        <p
          class="thumbBox"
          @click="thumb('down')"
          :class="{ dislikeToggle: dislikeToggle }"
        >
          <font-awesome-icon :icon="['fas', 'thumbs-down']" />
        </p>
        <p class="number">{{ dislike }}</p>
      </div>
    </div>
    <div class="bar">
      <div class="barBox"></div>
      <div class="fillBox" :style="{ width: ratio * 100 + '%' }"></div>
    </div>
    <div class="delete" @click="deleteCard">
      <font-awesome-icon :icon="['fas', 'trash']" />
    </div>
  </div>
</template>

<script>
export default {
  props: [
    "title",
    "category",
    "like",
    "dislike",
    "id",
    "userLike",
    "userDislike",
  ],
  data() {
    return {
      likeValue: this.like,
      dislikeValue: this.dislike,
      likeToggle: false,
      dislikeToggle: false,
    };
  },
  computed: {
    ratio() {
      return this.like / (this.likeValue + this.dislikeValue);
    },
  },
  methods: {
    thumb(event) {
      if (event == "down") {
        if (!this.dislikeToggle && !this.likeToggle) {
          this.$emit("THUMB", {
            event: event,
            id: this.id,
            first: true,
          });
        } else if (!this.dislikeToggle) {
          this.$emit("THUMB", {
            event: event,
            id: this.id,
            first: false,
          });
        }
        this.dislikeToggle = true;
        this.likeToggle = false;
      } else {
        if (!this.dislikeToggle && !this.likeToggle) {
          this.$emit("THUMB", {
            event: event,
            id: this.id,
            first: true,
          });
        } else if (!this.likeToggle) {
          this.$emit("THUMB", {
            event: event,
            id: this.id,
            first: false,
          });
        }
        this.dislikeToggle = false;
        this.likeToggle = true;
      }
    },
    deleteCard() {
      this.$emit("DELETE", {
        id: this.id,
      });
    },
  },
  watch: {
    like: {
      // the callback will be called immediately after the start of the observation
      immediate: true,
      handler(val) {
        this.likeValue = val;
      },
    },
    dislike: {
      // the callback will be called immediately after the start of the observation
      immediate: true,
      handler(val) {
        this.dislikeValue = val;
      },
    },
  },
  mounted: function() {
    if (this.userLike) {
      this.likeToggle = true;
    } else if (this.userDislikee) {
      this.dislikeToggle = true;
    }
  },
};
</script>

<style>
.cardBox {
  max-width: 350px;
  width: 80%;
  height: 450px;
  border: none;
  border-radius: 15px;
  box-shadow: 0px 0px 15px rgb(223, 223, 223);
  margin: 50px;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: space-around;
}
.categorie {
  font-size: 18px;
}
.thumbs {
  width: 100%;
  font-size: 30px;
  display: flex;
  align-items: center;
  justify-content: space-around;
  height: 40px;
  color: white;
  cursor: pointer;
}
.likeToggle {
  background: #66bb6a !important;
  padding: 5px 20px !important;
  border-radius: 10px !important;
  color: white !important;
}
h1 {
  margin: 0;
  padding: 0;
}
.dislikeToggle {
  background: #e53935 !important;
  padding: 5px 20px !important;
  border-radius: 10px !important;
  color: white !important;
}
.thumbBox {
  background: white;
  color: #424242;
  padding: 5px 20px;
  border-radius: 10px;
}
.thumbBox {
  transition: all ease 200ms;
  position: relative;
}

.delete:hover {
  transform: scale(1.05);
}
.thumbBox:hover {
  transform: rotate(10deg) scale(1.1);
}
span:nth-child(1) {
  color: #66bb6a;
}
.left .number {
  color: #66bb6a;
}
.right .number {
  color: #e53935;
}
.number {
  margin-left: 10px;
  position: absolute;
  bottom: -40px;
  right: 25px;
}
.left,
.right {
  display: flex;
  position: relative;
  align-items: center;
  justify-content: space-between;
}
.bar {
  width: 80%;
  height: 10px;
  border-radius: 10px;
  margin: 20px 0;
  position: relative;
  overflow: hidden;
}
.barBox {
  position: absolute;
  top: 0;
  left: 0;
  height: 100%;
  border-radius: 10px;
  width: 100%;
  background: #e53935;
  z-index: 1;
}
.fillBox {
  top: 0;
  left: 0;
  width: 80%;
  height: 100%;
  position: absolute;
  background: #66bb6a;
  border-top-left-radius: 20px;
  z-index: 2;
  border-bottom-left-radius: 20px;
}
.delete {
  font-size: 30px;
  background: #ffa000;
  color: white;
  padding: 7px 30px;
  border-radius: 20px;
  cursor: pointer;
  transition: all ease 200ms;
}
</style>
