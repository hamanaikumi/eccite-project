<template>
  <div>
    <div class="bg_pattern Boxes"></div>
    <div class="top-wrapper">
      <div class="register">Sign up</div>
      <div class="container">
        <div class="row register-page">
          <span class="error">{{ errorOfName }}</span>
          <div class="row">
            <div class="input-field col s6">
              <input
                id="last_name"
                type="text"
                class="validate"
                required
                v-model="lastName"
              />
              <label for="last_name" v-show="!hasErrors">姓</label>
            </div>
            <div class="input-field col s6">
              <input
                id="first_name"
                type="text"
                class="validate"
                required
                v-model="firstName"
              />
              <label for="first_name" v-show="!hasErrors">名</label>
            </div>
          </div>
          <div class="row">
            <div class="input-field col s12">
              <span class="error">{{ errorOfMailAddress }}</span>
              <input
                id="email"
                type="email"
                class="validate"
                required
                v-model="mailAddress"
              />

              <label for="email" v-show="!hasErrors">メールアドレス</label>
            </div>
          </div>
          <div class="row">
            <div class="input-field col s12">
              <span class="error">{{ errorOfZipCode }}</span>
              <input id="zipcode" type="text" maxlength="8" v-model="zipCode" />
              <label for="zipcode" v-show="!hasErrors"
                >郵便番号(ハイフンあり)</label
              >
              <button class="btn" type="button" v-on:click="getAddress">
                <span>住所検索</span>
              </button>
            </div>
          </div>
          <div class="row">
            <div class="input-field col s12">
              <span class="error">{{ errorOfAddress }}</span>
              <input id="address" type="text" v-model="address" />
              <label
                for="address"
                v-if="hasErrors === false && addressFlug === true"
                >住所</label
              >
            </div>
          </div>
          <div class="row">
            <div class="input-field col s12">
              <span class="error">{{ errorOfPhone }}</span>
              <input id="tel" type="tel" v-model="phoneNumber" />
              <label for="tel" v-show="!hasErrors"
                >電話番号(ハイフンあり)</label
              >
            </div>
          </div>
          <div class="row">
            <div class="input-field col s12">
              <span class="error">{{ errorOfPassword }}</span>
              <input
                id="password"
                type="password"
                class="validate"
                minlength="8"
                required
                v-model="password"
              />
              <label for="password" v-show="!hasErrors">パスワード</label>
            </div>
          </div>
          <div class="row">
            <div class="input-field col s12">
              <span class="error">{{ errorOfCheckPassword }}</span>
              <input
                id="confirmation_password"
                type="password"
                class="validate"
                minlength="8"
                required
                v-model="checkPassword"
              />
              <label for="confirmation_password" v-show="!hasErrors"
                >確認用パスワード</label
              >
            </div>
          </div>
          <div class="row register-admin-btn">
            <button class="btn" type="button" v-on:click="registerAdmin">
              <span>登録<i class="material-icons right">done</i></span>
            </button>

            <span class="clearBtn"
              ><button class="btn" type="button" v-on:click="clear">
                クリア
              </button></span
            >
          </div>
        </div>
      </div>
      <!-- end container-->
    </div>
  </div>
</template>

<script lang="ts">
import { Component, Vue } from "vue-property-decorator";
import axios from "axios";

@Component
export default class RegisterAdmin extends Vue {
  //姓
  private lastName = "";
  //名
  private firstName = "";
  //名前のエラーメッセージ
  private errorOfName = "";
  //メールアドレス
  private mailAddress = "";
  //メールのエラーメッセージ
  private errorOfMailAddress = "";
  //住所
  private address = "";
  //住所のエラーメッセージ
  private errorOfAddress = "";
  //郵便番号から住所検索
  private addressFlug = true;
  //電話番号
  private phoneNumber = "";
  //電話番号のエラーメッセージ
  private errorOfPhone = "";
  //パスワード
  private password = "";
  //パスワードのエラーメッセージ
  private errorOfPassword = "";
  //確認用パスワード
  private checkPassword = "";
  //確認用パスのエラーメッセージ
  private errorOfCheckPassword = "";
  //郵便番号
  private zipCode = "";
  //郵便番号のエラーメッセージ
  private errorOfZipCode = "";
  //エラーチェック
  private hasErrors = false;
  //responseのerrorCodeE-02のエラーメッセージ
  private errorMessage = "";

  /**
   * 登録したいユーザー情報を外部APIに送る.
   */
  async registerAdmin(): Promise<void> {
    //エラーメッセージを初期化
    this.errorOfName = "";
    this.errorOfMailAddress = "";
    this.errorOfAddress = "";
    this.errorOfPhone = "";
    this.errorOfPassword = "";
    this.errorOfCheckPassword = "";
    this.errorOfZipCode = "";
    this.hasErrors = false;

    //入力値エラーチェックし、エラーがあればメッセージを表示させreturn
    if (this.lastName === "" || this.firstName === "") {
      this.errorOfName = "姓または名が入力されていません";
      this.hasErrors = true;
    }
    if (this.mailAddress === "") {
      this.errorOfMailAddress = "メールアドレスが入力されていません";
      this.hasErrors = true;
    } else if (this.mailAddress.indexOf("@") === -1) {
      this.errorOfMailAddress = "メールアドレスの形式が不正です";
      this.hasErrors = true;
    }
    if (this.address === "") {
      this.errorOfAddress = "住所が入力されていません";
      this.hasErrors = true;
    }
    if (this.phoneNumber === "") {
      this.errorOfPhone = "電話番号が入力されていません";
      this.hasErrors = true;
    } else if (this.phoneNumber.indexOf("-") === -1) {
      this.errorOfPhone = "電話番号はハイフンを入れて入力してください";
      this.hasErrors = true;
    }
    if (this.password === "") {
      this.errorOfPassword = "パスワードが入力されていません";
      this.hasErrors = true;
    }
    if (this.password.length < 8 || this.password.length > 16) {
      this.errorOfPassword =
        "パスワードは８文字以上１６文字以内で設定してください";
      this.hasErrors = true;
    }
    if (
      !(
        /[A-Z]+/.test(this.password) &&
        /[a-z]+/.test(this.password) &&
        /[0-9]+/.test(this.password)
      )
    ) {
      this.errorOfPassword =
        "パスワードは大文字/小文字/数字を組み合わせて下さい。";
      this.hasErrors = true;
    }

    if (this.password !== this.checkPassword) {
      // パスワード一致チェック
      this.errorOfCheckPassword = "パスワードが不一致です";
      this.hasErrors = true;
    }
    if (this.checkPassword === "") {
      this.errorOfCheckPassword = "確認用パスワードを入力してください";
      this.hasErrors = true;
    }
    if (this.zipCode === "") {
      this.errorOfZipCode = "郵便番号が入力されていません";
      this.hasErrors = true;
    } else if (this.zipCode.indexOf("-") === -1) {
      this.errorOfZipCode = "郵便番号はハイフンを入れて入力してください";
      this.hasErrors = true;
    }

    if (this.hasErrors === true) {
      return;
    }

    //ユーザー登録処理
    const response = await axios.post(
      "http://153.127.48.168:8080/ecsite-api/user",
      {
        name: this.lastName + " " + this.firstName,
        email: this.mailAddress,
        password: this.password,
        checkPassword: this.checkPassword,
        zipcode: this.zipCode,
        address: this.address,
        telephone: this.phoneNumber,
      }
    );
    if (response.data.status === "success") {
      this["$router"].push("/login");
    } else if (response.data.errorCode === "E-01") {
      this.errorOfMailAddress = response.data.message;
    } else if (response.data.errorCode === "E-02") {
      this.errorMessage = response.data.message;
    } else {
      throw new Error("予期しないエラーが発生しました");
    }
  }

  /**
   * 郵便番号から住所を取得.
   */
  async getAddress(): Promise<void> {
    //初期値リセット(住所、住所エラー)
    this.address = "";
    this.errorOfAddress = "";
    this.addressFlug = true;
    //郵便番号から住所を取得APIに郵便番号を送る
    try {
      // const axios = require("axios");
      // eslint-disable-next-line @typescript-eslint/no-var-requires
      const axiosJsonpAdapter = require("axios-jsonp");

      const response = await axios.get("https://zipcoda.net/api", {
        adapter: axiosJsonpAdapter,
        params: {
          zipcode: this.zipCode,
        },
      });
      //成功したら住所に取得したデータを代入
      if (response.data.length === 1) {
        this.address =
          response.data.items[0].state_name + response.data.items[0].address;
        this.addressFlug = false;
        //失敗したらエラーを出す
      } else {
        this.errorOfAddress = "住所が見つかりません";
        this.addressFlug = false;
      }
    } catch (e) {
      this.errorOfAddress = "住所が見つかりません";
      this.addressFlug = false;
    }
  }

  /**
   * 入力した情報をリセットする.
   */
  clear(): void {
    this.lastName = "";
    this.firstName = "";
    this.errorOfName = "";
    this.mailAddress = "";
    this.errorOfMailAddress = "";
    this.address = "";
    this.errorOfAddress = "";
    this.phoneNumber = "";
    this.errorOfPhone = "";
    this.password = "";
    this.errorOfPassword = "";
    this.checkPassword = "";
    this.errorOfCheckPassword = "";
    this.zipCode = "";
    this.errorOfZipCode = "";
  }
}
</script>
<style scoped>
.top-wrapper {
  padding-top: 10px;
}
.register {
  text-align: center;
  padding-bottom: 10px;
  color: navy;
  font-weight: bolder;
  font-size: 45px;
}
.clearBtn {
  margin-left: 10px;
}
.bg_pattern {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  background-color: #ffe9a7;
  opacity: 0.4;
  z-index: -1;
}
.Boxes {
  background-image: linear-gradient(#ffc107 2px, transparent 2px),
    linear-gradient(to right, #ffc107 2px, #ffe9a7 2px);
  background-size: 40px 40px;
}
.register-page {
  background-color: white;
  box-shadow: 0 2px 2px 0 rgb(0 0 0 / 14%), 0 3px 1px -2px rgb(0 0 0 / 12%),
    0 1px 5px 0 rgb(0 0 0 / 20%);
}
</style>
