<template>

  <div class="home" v-if="isLogin">
    <h1 class="home__title">
      所得稅計算機
    </h1>
    <form>
      <div class="mb-3">
        <label>該年度個人薪資總額: {{salary}}</label>
        <b-input-group prepend="0" append="10000000" class="mt-3">
            <b-form-input type="range" min="0" max="10000000" v-model="salary"></b-form-input></b-form-input>
        </b-input-group>
      </div>
      <div class="mb-3">
        <label>免稅額: {{notax}}</label>
          <b-form-checkbox v-model="isSeven">
            是否已滿70歲，或有滿該歲數之配偶或直系尊親屬	
          </b-form-checkbox>
          <b-form-checkbox v-model="isSingle">
            是否單身
          </b-form-checkbox>
      </div>
      <div class="mb-3">
        <label>標準列舉扣除額</label>
        <b-input-group prepend="$">
            <b-form-input v-model="standard"></b-form-input>
        </b-input-group>
      </div>
      <div class="mb-3">
        <label>特別扣除額</label>
        <b-input-group prepend="$">
            <b-form-input v-model="special"></b-form-input>
        </b-input-group>
      </div>
      <div class="mb-3">
        <label>基本生活費</label>
        <b-input-group prepend="$">
            <b-form-input v-model="basic"></b-form-input>
        </b-input-group>
      </div>
    </form>
    <hr>
    <p>
      <b-icon icon="exclamation-circle-fill" variant="warning"></b-icon>
      稅額計算方法： <br>(所得總額 – 免稅額 – 標準/列舉扣除額 – 特別扣除額 – 基本生活費) * 所得稅率
    </p>
    <h2>應繳稅額 = {{tax}}</h2>
    
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
  text-align: left;
}

.home__title {
  font-size: 6rem;
  line-height: 1.15;
}

.home_subtitle {
  font-size: 2rem;
  line-height: 2;
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
import Vue from 'vue'
import { BootstrapVue, IconsPlugin } from 'bootstrap-vue'

// Import Bootstrap and BootstrapVue CSS files (order is important)
import 'bootstrap/dist/css/bootstrap.css'
import 'bootstrap-vue/dist/bootstrap-vue.css'

// Make BootstrapVue available throughout your project
Vue.use(BootstrapVue)
// Optionally install the BootstrapVue icon components plugin
Vue.use(IconsPlugin)


export default {
  data: function() {
    return {
      version: packageJson.version,
      liffError: "",
      inputvalue: "0",
      isLogin: null,
      salary: "0",
      isSingle: false,
      isSeven: false,
      standard: "0",
      special: "0",
      basic: "0"
    };
  },
  computed: {
    notax(){
      const singleFee = this.isSingle ? 120000 : 240000;
      const sevenFee = this.isSeven ? 132000 : 88000;
      return singleFee + sevenFee
    },
    tax(){
      const netIncome = this.salary - this.notax - this.standard - this.special - this.basic;
      let ratio = 0.0
      if(netIncome <= 540000) ratio = 0.05;
      else if(netIncome <= 121000) ratio = 0.12;
      else if(netIncome <= 2420000) ratio = 0.2;
      else if(netIncome <= 4530000) ratio = 0.3;
      else ratio = 0.4

      return Math.round(netIncome * ratio >= 0 ? netIncome * ratio : 0)
    }
  },
  mounted() {
    // mounted() is rendered when DOM is rendered
    // wait liff.init()
    this.$liffInit
      .then(() => { 
        if (!liff.isLoggedIn()) {
            console.log("你還沒登入Line哦！");
            liff.login({ redirectUri: "https://daily-up.herokuapp.com/" });
            
          } else {
            console.log("你已經登入Line哦！");
        }
        this.isLogin = liff.isLoggedIn();
      })
      .catch((error) => {
        this.liffError = error;
      });
  }
};
</script>
