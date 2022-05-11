<template>
  <div>
    <section
      class="header flex flex-row justify-between items-center bg-slate-200 py-3 px-5"
    >
      <h4 class="text-md">Hi {{ user }}</h4>
      <svg
        class="w-4 h-4"
        fill="none"
        stroke="currentColor"
        viewBox="0 0 24 24"
        xmlns="http://www.w3.org/2000/svg"
        @click="logout"
      >
        <path
          stroke-linecap="round"
          stroke-linejoin="round"
          stroke-width="2"
          d="M17 16l4-4m0 0l-4-4m4 4H7m6 4v1a3 3 0 01-3 3H6a3 3 0 01-3-3V7a3 3 0 013-3h4a3 3 0 013 3v1"
        ></path>
      </svg>
    </section>
    <section class="mt-10 flex flex-col items-center justify-center">
      <h2 class="text-lg">Highest Score: {{ highScore }}</h2>
      <TrafficLight class="mt-10" :color="trafficColor" />
      <h2 class="text-lg mt-2">Score: {{ currentScore }}</h2>
      <div class="flex flex-row w-10/12 mx-auto justify-evenly mt-5">
        <button
          @click="walkLeft"
          class="rounded bg-slate-700 w-2/5 flex flex-row items-center justify-evenly text-white p-3"
        >
          <svg
            class="w-6 h-6"
            fill="none"
            stroke="currentColor"
            viewBox="0 0 24 24"
            xmlns="http://www.w3.org/2000/svg"
          >
            <path
              stroke-linecap="round"
              stroke-linejoin="round"
              stroke-width="2"
              d="M10 19l-7-7m0 0l7-7m-7 7h18"
            ></path>
          </svg>
          LEFT
        </button>
        <button
          @click="walkRight"
          class="rounded bg-slate-700 w-2/5 flex flex-row items-center justify-evenly text-white p-3"
        >
          RIGHT
          <svg
            class="w-6 h-6"
            fill="none"
            stroke="currentColor"
            viewBox="0 0 24 24"
            xmlns="http://www.w3.org/2000/svg"
          >
            <path
              stroke-linecap="round"
              stroke-linejoin="round"
              stroke-width="2"
              d="M14 5l7 7m0 0l-7 7m7-7H3"
            ></path>
          </svg>
        </button>
      </div>
    </section>
  </div>
</template>

<script>
import TrafficLight from "./TrafficLight.vue";
export default {
  components: {
    TrafficLight,
  },
  data() {
    return {
      user: "",
      currentScore: 0,
      highScore: 0,
      trafficLight: "green",
      trafficColor: "fill-green-500",
      lastStep: null,
      currentStep: "",
      calculatedTime: 0,
      userExisted: false,
    };
  },
  mounted() {
    this.user = localStorage.getItem("user");
    this.savedUsers = JSON.parse(localStorage.getItem("users"));
    if (this.savedUsers) {
      const currentUser = this.savedUsers.filter((user) => {
        if (user.user === this.user) {
          return user;
        }
      });
      if (currentUser.length > 0) {
        this.userExisted = true;
        this.highScore = currentUser[0].score;
      }
    }
    this.changeColor();
  },
  methods: {
    logout() {
      localStorage.removeItem("user");
      if (this.userExisted) {
        const newUsers = this.savedUsers.map((user) => {
          if (user.user === this.user) {
            return {
              user: this.user,
              score: this.highScore,
            };
          } else return user;
        });
        localStorage.setItem("users", JSON.stringify(newUsers));
      } else if (!this.savedUsers) {
        localStorage.setItem(
          "users",
          JSON.stringify([
            {
              user: this.user,
              score: this.highScore,
            },
          ])
        );
      } else {
        this.savedUsers.push({
          user: this.user,
          score: this.highScore,
        });
        localStorage.setItem("users", JSON.stringify(this.savedUsers));
      }
      this.$router.push("/login");
    },
    changeColor() {
      this.calculateTime();
      if (this.trafficLight === "red") {
        setTimeout(() => {
          this.trafficLight = "green";
          this.trafficColor = "fill-green-500";
          this.changeColor();
        }, 3000);
      } else {
        setTimeout(() => {
          this.trafficLight = "red";
          this.trafficColor = "fill-red-500";
          this.changeColor();
        }, this.calculatedTime);
      }
    },
    calculateTime() {
      const items = [1500, -1500];
      const randomNum = items[Math.floor(Math.random() * 2)];
      const time = 10000 - this.currentScore * 100 + randomNum;
      if (time < 2000) {
        this.calculatedtime = 2000;
      } else {
        this.calculatedTime = time;
      }
    },
    walkLeft() {
      this.currentStep = "left";
      if (this.trafficLight === "red") {
        this.currentScore = 0;
      } else if (this.lastStep === this.currentStep) {
        this.currentScore--;
      } else {
        this.currentScore++;
      }
      this.lastStep = "left";
      this.setHighScore();
    },
    walkRight() {
      this.currentStep = "right";
      if (this.trafficLight === "red") {
        this.currentScore = 0;
      } else if (this.lastStep === this.currentStep) {
        this.currentScore--;
      } else {
        this.currentScore++;
      }
      this.lastStep = "right";
      this.setHighScore();
    },
    setHighScore() {
      if (this.currentScore > this.highScore) {
        this.highScore = this.currentScore;
      }
    },
  },
};
</script>

<style></style>
