<template>
  <div class="home">
  <div v-if="!isLogin">
    <p>沒登入嗚嗚嗚</p>
    <button>點這邊登入！</button>
  </div>
  <div v-else>
      <h1 class="home__title">
      所得稅計算機
    </h1>
    <p>使用者姓名：{{displayName}}</p>
    
    <p>應繳稅額 = {{tax}}</p>
    <div class="home__badges">
      <span class="home__badges__badge badge--primary"> 每日推播 </span>
      <span class="home__badges__badge badge--secondary"> nuxtjs </span>
      <span class="home__badges__badge badge--primary"> {{ version }} </span>
      <a
        href="https://github.com/line/line-liff-v2-starter"
        target="_blank"
        rel="noreferrer"
        class="home__badges__badge badge--secondary"
      >
        GitHub
      </a>
    </div>
    <p>月收入 = {{inputvalue}}</p>
    <input type="range" min="1" max="100000" v-model:value="inputvalue"/>
    <br>
    <input type="checkbox" v-model:value="isforeign">是否為外國人
  </div>
  </div>
</template>

<style>
html,
body {
  font-family: -apple-system, BlinkMacSystemFont, Segoe UI, Roboto, Oxygen,
    Ubuntu, Cantarell, Fira Sans, Droid Sans, Helvetica Neue, sans-serif;
  margin: 0;
  padding: 0;
}

.home {
  padding: 5rem 2rem;
  text-align: center;
}

.home__title {
  font-size: 6rem;
  line-height: 1.15;
}

.home__title__link {
  color: #06c755;
  text-decoration: none;
}

.home__title__link:hover {
  text-decoration: underline;
}

.home__badges {
  align-items: center;
  justify-content: center;
  display: flex;
  flex-wrap: nowrap;
  overflow: hidden;
  margin-bottom: 6rem;
}

.home__badges__badge:first-child {
  border-top-left-radius: 2px;
  border-bottom-left-radius: 2px;
}

.home__badges__badge:last-child {
  border-top-right-radius: 2px;
  border-bottom-right-radius: 2px;
}

.home__badges__badge {
  display: inline-block;
  padding: 0.3em 0.4em;
  font-size: 0.75rem;
  font-weight: 700;
  line-height: 1;
  text-align: center;
  white-space: nowrap;
  vertical-align: baseline;
}

.badge--primary {
  color: #353a40;
  border: 1px solid #353a40;
  background-color: transparent;
  padding-top: calc(0.3em - 1px);
  padding-bottom: calc(0.3em - 1px);
}

.badge--secondary {
  color: #fff;
  background-color: #353a40;
}

.home__buttons {
  display: flex;
  align-items: center;
  justify-content: center;
  flex-wrap: wrap;
  gap: 1rem;
}

.home__buttons__button {
  min-width: 250px;
  cursor: pointer;
  display: inline-block;
  font-weight: 400;
  text-align: center;
  vertical-align: middle;
  user-select: none;
  border: 1px solid transparent;
  padding: 0.375rem 0.75rem;
  font-size: 0.9375rem;
  line-height: 1.5;
  border-radius: 2px;
  text-decoration: none;
  transition: color 0.15s ease-in-out, background-color 0.15s ease-in-out,
    border-color 0.15s ease-in-out, box-shadow 0.15s ease-in-out;
}

.button--primary {
  color: #fff;
  background-color: #00b900;
  border-color: #00b900;
}

.button--primary:hover {
  color: #fff;
  background-color: #009300;
  border-color: #008600;
}

.button--secondary {
  color: #fff;
  background-color: #353a40;
  border-color: #353a40;
}

.button--secondary:hover {
  color: #fff;
  background-color: #24272b;
  border-color: #1e2124;
}

.button--tertiary {
  background-color: transparent;
  background-image: none;
  color: #353a40;
  border-color: #353a40;
}

.button--tertiary:hover {
  color: #353a40;
  background-color: rgba(53, 58, 64, 0.1);
  border-color: #353a40;
}

@media screen and (max-width: 600px) {
  html {
    font-size: 12px;
  }

  .home__title {
    font-size: 4rem;
    line-height: 1.15;
  }

  .home__buttons__button {
    font-size: 1.5rem;
  }
}

@media screen and (max-width: 930px) {
  .home__buttons {
    flex-direction: column;
  }
}
</style>

<script>
import packageJson from "../package.json";


export default {
  data: function() {
    return {
      version: packageJson.version,
      sdkVersion: "",
      liffError: "",
      displayName: "",
      inputvalue: "0",
      isforeign: false,
      isLogin: false
    };
  },
  computed: {
    tax(){
      const ratio = this.isforeign ? 0.1 : 0.2;
      return Math.round(this.inputvalue * ratio)
    }
  },
  mounted() {
    // mounted() is rendered when DOM is rendered
    // wait liff.init()
    this.$liffInit
      .then(() => {
        //update needed data
        this.sdkVersion = liff.getVersion();
        
        liff
        .getProfile()
        .then((profile) => {
          this.displayName = profile.displayName;
          this.isLogin = true;
        })
        .catch((err) => {
          console.log("error", err);
        });
      })
      .catch((error) => {
        this.liffError = error;
      });
  }
};
</script>
