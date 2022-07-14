<template>
  <div class="home" v-if="isLogin">
    <h1 class="home__title">
      所得稅計算機
    </h1>
    <h2 class="home_subtitle">該年度個人薪資總額</h2>
    <p>{{salary}}</p>
    <input type="range" min="1" max="10000000" v-model:value="salary"/>
    <h2 class="home_subtitle">免稅額</h2>
    <input type="checkbox" v-model:value="isSeven">是否有年滿70歲之納稅義務人、配偶及受納稅義務人扶養之直系尊親屬	
    <br>
    <input type="checkbox" v-model:value="isSingle">是否單身	
    <h2 class="home_subtitle">標準列舉扣除額</h2>
    <p>{{standard}}</p>
    <input type="range" min="1" max="100000" v-model:value="standard"/>
    <h2 class="home_subtitle">特別扣除額</h2>
    <p>{{special}}</p>
    <input type="range" min="1" max="100000" v-model:value="special"/>
    <h2 class="home_subtitle">基本生活費</h2>
    <p>{{basic}}</p>
    <input type="range" min="1" max="100000" v-model:value="basic"/>
    <hr>
    <p>稅額計算方法： (所得總額 – 免稅額 – 標準/列舉扣除額 – 特別扣除額 – 基本生活費) * 所得稅率</p>
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
    tax(){
      const singleFee = this.isSingle ? 240000 : 120000;
      const sevenFee = this.isSeven ? 88000 : 132000;
      const netIncome = this.salary - singleFee - sevenFee - this.standard - this.special - this.basic;
      let ratio = 0.0
      switch (netIncome) {
        case netIncome <= 540000:
          ratio = 0.05
          break;
        case netIncome <= 1210000:
          ratio = 0.12
          break;
        case netIncome <= 2420000:
          ratio = 0.2
          break;
        case netIncome <=45300000:
          ratio = 0.3
          break;
        default:
          ratio = 0.4
          break;
      }
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
            liff.login();
            
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
