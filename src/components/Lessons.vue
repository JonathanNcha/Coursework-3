<template>
  <div>
    <div v-for="lesson in sortedLessons" :key="lesson.id" class="sum">
      <div class="card border-primary w-20">
        <div class="card-body">
          <div class="aligned">
            <i :class="lesson.image"></i>
            <span class="line">
              <br /><br />
              <p>Subject: {{ lesson.title }}</p>
              <p>Location: {{ lesson.location }}</p>
              <p>Price: {{ lesson.price | currency }}</p>
              <p>
                availableSpace:
                {{ lesson.availableSpace - cartCount(lesson.id) }}
              </p>
            </span>
          </div>

          <div class="text-center mt-2">
            <button
              class="btn btn-primary btn-sm w-50"
              v-on:click="addToCart(lesson)"
              v-if="flag"
            >
              Add to cart
            </button>
            <button
              class="btn btn-primary btn-sm w-50"
              disabled="disabled"
              v-else
            >
              Add to cart
            </button>
          </div>
        </div>
      </div>
      <br>
    </div>
  </div>
</template>

<script>
export default {
  name: "Lessons",
  props: {
    sortedLessons: Array
  },
  data() {
    return {};
  },
  methods: {
    addToCart(lesson) {
      this.$emit("addCart", lesson);
    },
    canAddToCart(lesson) {
      return lesson.availableSpace > this.cartCount(lesson.id);
    },

    cartCount(id) {
      let count = 0;
      for (let i = 0; i < this.lessonCart.length; i++) {
        if (this.lessonCart[i] === id) {
          count++;
        }
      }
      return count;
    },
  },
};
</script>