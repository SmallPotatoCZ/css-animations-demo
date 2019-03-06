<template>
  <div class="player">
    <div class="current-track">
      <img class="current-track-image" src="../assets/zc.jpg" width="32" height="32" alt>
      <span class="track-name">name</span>
    </div>

    <div class="controls">
      <!-- Previous track button -->
      <button>
        <svg xmlns="http://www.w3.org/2000/svg" width="17" height="17" viewBox="0 0 16 16">
          <path fill="#fff" d="M14 15V1L4 8zM2 1h2v14H2V1z"></path>
        </svg>
      </button>
      <!-- Play/pause button -->
      <button class="play-button" @click="togglePlay">
        <svg width="16" height="20" viewBox="0 0 42 40" xmlns="http://www.w3.org/2000/svg">
          <polygon ref="shape1" fill="#fff" :points="this.buttonShape.play1"></polygon>
          <polygon ref="shape2" fill="#fff" :points="this.buttonShape.play2"></polygon>
        </svg>
      </button>
      <!-- Next track button -->
      <button>
        <svg xmlns="http://www.w3.org/2000/svg" width="17" height="17" viewBox="0 0 16 16">
          <path fill="#fff" d="M2 1v14l10-7zM12 1h2v14h-2V1z"></path>
        </svg>
      </button>
    </div>

    <div class="list"></div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      isFavourite: false,
      volumeLevel: 2,
      isPlaying: false,
      buttonShape: {
        play1: [0, 0, 0, 40, 18.032, 29.982, 17.971, 9.984],
        play2: [18.032, 29.982, 35.823, 20.099, 36, 20, 17.971, 9.984],
        pause1: [0, 0, 0, 40, 11, 40, 11, 0],
        pause2: [21, 40, 32, 40, 32, 0, 21, 0]
      }
    };
  },

  methods: {
    togglePlay() {
      const shapes = this.buttonShape;

      const getProgress = ({ elapsed, total }) => Math.min(elapsed / total, 1);

      const time = {
        start: performance.now(),
        total: 150
      };

      const morph = now => {
        time.elapsed = now - time.start;

        const getPoints = (from, to) =>
          from.map((start, index) => {
            const end = to[index];
            const distance = end - start;
            const point = start + getProgress(time) * distance;
            return point;
          });

        const points1 = this.isPlaying
          ? getPoints(shapes.play1, shapes.pause1)
          : getPoints(shapes.pause1, shapes.play1);
        const points2 = this.isPlaying
          ? getPoints(shapes.play2, shapes.pause2)
          : getPoints(shapes.pause2, shapes.play2);

        this.$refs.shape1.setAttribute("points", points1.join(" "));
        this.$refs.shape2.setAttribute("points", points2.join(" "));

        if (getProgress(time) < 1) requestAnimationFrame(morph);
      };

      requestAnimationFrame(morph);
      this.isPlaying = !this.isPlaying;
    },

    changeVolume() {
      this.volumeLevel = this.volumeLevel === 3 ? 0 : this.volumeLevel + 1;
    },

    toggleFavourite() {
      this.isFavourite = !this.isFavourite;
    }
  }
};
</script>


<style scoped>
.player {
  position: fixed;
  left: 0;
  bottom: 0;
  z-index: 1;
  display: flex;
  height: 4rem;
  width: 100%;
  background-color: #3b3c4b;
  align-items: center;
}

.current-track {
  width: 40%;
  padding: 0 0.5rem;
}

.current-track > img {
  vertical-align: middle;
}

.current-track > .track-name {
  vertical-align: middle;
  padding: 0 0.5rem;
}

.controls {
  display: flex;
  justify-content: center;
  width: 50%;
}

.controls > button {
  outline: none;
  margin: 1rem;
}
.list {
  width: 10%;
}
</style>
