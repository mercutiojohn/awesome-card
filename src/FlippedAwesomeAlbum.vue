<template>
  <div class="playground-box" @click="handleClickBack()">
    <div ref="container" class="tilt-container" @click.stop="handleClickAlbum()">
      <div class="tilt-wrapper" ref="wrapper" :class="{ flipped: isFlipped }">
        <div class="tilt-div" ref="div">
          <div class="back"></div>
          <div class="front">
            <div class="album-cover-image">
                <img :src="cover" alt="Album Cover" />
            </div>
          </div>
          <div class="overlay"></div>
        </div>
      </div>
      <div class="tilt-flipped" :class="{ flipped: isFlipped }">
        <div class="album-info">
            <h3>{{ title }}</h3>
            <p>{{ artist }}</p>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      containerEl: null,
      wrapperEl: null,
      divEl: null,
      overlayEl: null,
      //
      containerRect: "",
      mouseX: "",
      mouseY: "",
      //
      tiltX: "",
      tiltY: "",
      //
      wrapperRect: "",
      wrapperCenterX: "",
      wrapperCenterY: "",
      //
      translateX: "",
      translateY: "",
      // test
      title: 'aaa',
      artist: 'bbb',
      cover: 'https://picsum.photos/100/100?random=1',
      isFlipped: false
    };
  },
  mounted() {
    this.containerEl = this.$refs.container;
    this.wrapperEl = this.$refs.wrapper;
    this.divEl = this.$refs.div;
    this.overlayEl = this.divEl.querySelector(".overlay");

    this.containerEl.addEventListener("mousemove", this.handleMouseMove);
    this.containerEl.addEventListener("mousedown", this.handleMouseDown);
    this.containerEl.addEventListener("mouseup", this.handleMouseUp);
    this.containerEl.addEventListener("mouseout", this.handleMouseOut);
  },
  beforeUnmount() {
    this.containerEl.removeEventListener("mousemove", this.handleMouseMove);
    this.containerEl.removeEventListener("mouseout", this.handleMouseOut);
  },
  watch:{
    isFlipped(newVal) {
      this.handleMouseOut()
    }
  },
  methods: {
    handleClickAlbum() {
      this.isFlipped = true;
    },
    handleClickBack() {
      this.isFlipped = false;
    },
    calculate(event) {
      this.containerRect = this.containerEl.getBoundingClientRect();
      this.mouseX = event.clientX - this.containerRect.left;
      this.mouseY = event.clientY - this.containerRect.top;

      this.tiltX = (this.mouseY / this.containerRect.height - 0.5) * 20;
      this.tiltY = (this.mouseX / this.containerRect.width - 0.5) * 20;

      // this.wrapperEl.style.transform = `rotateX(${this.tiltX}deg) rotateY(${this.tiltY}deg) scale(0.97)`;
      // this.overlayEl.style.transform = `translate(${this.mouseX}px, ${this.mouseY}px)`;

      // this.wrapperRect = this.wrapperEl.getBoundingClientRect();
      // this.wrapperCenterX = this.wrapperRect.left + this.wrapperRect.width / 2;
      // this.wrapperCenterY = this.wrapperRect.top + this.wrapperRect.height / 2;

      // this.translateX = this.mouseX - this.wrapperCenterX;
      // this.translateY = this.mouseY - this.wrapperCenterY;

      // this.overlayEl.style.transformOrigin = `${-translateX}px ${-translateY}px`;
      // this.overlayEl.style.transform = `translate(${translateX}px, ${translateY}px)`;
    },
    handleMouseMove(event) {
      if(!this.isFlipped){
        this.calculate(event);

        this.wrapperEl.style.transform = `rotateX(${-this.tiltX}deg) rotateY(${
          this.tiltY
        }deg) scale(1.02)`;
        this.overlayEl.style.transform = `translate(${this.mouseX}px, ${this.mouseY}px)`;

        this.overlayEl.style.opacity = "1";

        // this.wrapperEl.style.transformStyle = "flat";
      }
    },
    handleMouseDown(event) {
      if(!this.isFlipped){
        this.calculate(event);

        this.wrapperEl.style.transform = `rotateX(${
          -this.tiltX * 1.4
        }deg) rotateY(${this.tiltY * 1.4}deg)`;
        this.overlayEl.style.transform = `translate(${this.mouseX}px, ${this.mouseY}px)`;
      }
    },
    handleMouseUp(event) {
      if(!this.isFlipped){
        this.calculate(event);

        this.wrapperEl.style.transform = `rotateX(${-this.tiltX}deg) rotateY(${
          this.tiltY
        }deg) scale(1.02)`;
        this.overlayEl.style.transform = `translate(${this.mouseX}px, ${this.mouseY}px)`;
      }
    },
    handleMouseOut() {
      if(!this.isFlipped){
        this.wrapperEl.style.transform = "";
        // this.overlayEl.style.transform = ``;
        this.overlayEl.style.opacity = "0";
      }
    },
  },
};
</script>

<style scoped>
.playground-box {
  width: 100vw;
  height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
}
.tilt-container {
  border-radius: 15px;
  transform-style: preserve-3d;
  transform: perspective(800px);
  width: 300px;
  height: 300px;
}

.tilt-wrapper,
.tilt-flipped {
  position: absolute;
  width: 100%;
  height: 100%;
  top: 0;
  left: 0;
  border-radius: 15px;
  /* transition: all 0.3s ease 0s; */
  overflow: hidden;
}
.tilt-wrapper {
  box-shadow: rgba(14, 21, 47, 0.6) 0px 3px 5px -2px;
}
.tilt-flipped {
  box-shadow: rgba(14, 21, 47, 0.6) 0px 3px 5px -2px;
}
.tilt-wrapper:hover {
  box-shadow: rgba(14, 21, 47, 0.6) 0px 18px 50px -3px;
}
.tilt-wrapper:active {
  box-shadow: rgba(14, 21, 47, 0.6) 0px 16px 40px -2px;
}
.tilt-div {
  position: absolute;
  width: 100%;
  height: 100%;
  /* transition: all 0.2s ease-out 0s; */
  border-radius: 15px;
}

/* flip */
.tilt-wrapper,
.tilt-flipped {
  backface-visibility: hidden;
  transition: transform 0.8s cubic-bezier(0.075, 0.82, 0.165, 1),
    opacity 0.6s ease 0.2s, box-shadow 0.8s ease;
}

.tilt-wrapper {
  transform: rotateY(0deg);
}
.tilt-wrapper.flipped {
  /* opacity: 0; */
  transform: rotateY(-180deg) scale(1.25)!important;
}
.tilt-flipped {
  /* opacity: 0; */
  transform: rotateY(180deg);
}

.tilt-flipped.flipped {
  /* opacity: 1; */
  transform: rotateY(0deg) scale(1.25);
  box-shadow: rgba(14, 21, 47, 0.6) 0px 16px 40px -2px;
}

.front {
  position: relative;
  width: 100%;
  height: 100%;
  border-radius: 15px;
  overflow: hidden;
  transform-style: preserve-3d;
}

.back {
  position: absolute;
  inset: 0px;
  border-radius: 15px;
  background: linear-gradient(
    135deg,
    rgba(255, 255, 255, 0.25) 0%,
    rgba(255, 255, 255, 0) 60%
  );
  background: #000;
}
.overlay:before {
  /* content: "";
  position: absolute;
  top: -50%;
  left: -50%;
  width: 200%;
  height: 200%;
  background: radial-gradient(
    ellipse farthest-corner at center,
    rgba(255, 255, 255, 0.8) 0%,
    rgba(255, 255, 255, 0) 70%
  );
  transform: scale(0);
  transition: transform 0.3s ease-out; */
}

.overlay:hover:before {
  /* transform: scale(1); */
}
.overlay {
  position: absolute;
  /* inset: 0px; */
  /* border-radius: 15px; */
  /* background: rgba(0, 0, 0, 0.4); */
  opacity: 0;
  top: -250px;
  left: -250px;
  /* width: 200%;
  height: 200%;
  top: 0;
  left: 0; */
  width: 500px;
  height: 500px;
  background: radial-gradient(
    ellipse farthest-corner at center,
    rgba(255, 255, 255, 0.4) 0%,
    rgba(255, 255, 255, 0) 80%
  );
  /* background: #ABC; */
  /* transform: scale(0); */
  transition: opacity 0.3s ease-out, transform 0.01s ease 0s;
  /* , transform 0.3s ease-out */
  /* pointer-events: none; */
}
.overlay:hover {
  /* transform: scale(1); */
  /* opacity: 0.8; */
}
.album-cover-image {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  transform-style: preserve-3d;
}
.album-cover-image img {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  object-fit: cover;
  backface-visibility: hidden;
}
</style>