<template>
  <div
    id="modal"
    :class="{ open: isOpen, processing: isProcessing, error: isError }"
  >
    <div class="dialog">
      <transition name="fade" mode="out-in">
        <div v-if="!isSuccess" key="form" class="container">
          <div class="headboard">
            <div class="item--title">{{ title }}</div>
            <div class="item--subtitle">{{ subtitle }}</div>
            <div class="close">
              <img
                src="~/static/images/close.svg"
                alt="Close modal"
                @click="onCancel"
              />
            </div>
          </div>
          <div class="main">
            <div class="field" :class="{ error: !!numberError }">
              <label for="card-number">Debit Card Number</label>
              <MaskedInput
                mask="card"
                id="card-number"
                type="tel"
                ref="number"
                placeholder="•••• •••• •••• ••••"
                v-model="cardNumber"
                @blur="validateField('number')"
                @input="validateField('number', true)"
              />
              <div class="field--error">{{ numberError }}</div>
            </div>
            <div class="field field--half" :class="{ error: !!expiryError }">
              <label>Expiry Date</label>
              <MaskedInput
                mask="expiry"
                id="card-expiry"
                type="tel"
                ref="expiry"
                placeholder="MM/YY"
                v-model="cardExpiry"
                @blur="validateField('expiry')"
                @input="validateField('expiry', true)"
              />
              <div class="field--error">{{ expiryError }}</div>
            </div>
            <div class="field field--half" :class="{ error: !!pinError }">
              <label for="card-pin">
                ATM PIN
                <img
                  src="~/static/images/question.svg"
                  title="The PIN number for your ATM card"
                />
              </label>
              <input
                id="card-pin"
                type="password"
                ref="pin"
                pattern="[0-9]*"
                inputmode="numeric"
                placeholder="••••"
                maxlength="6"
                v-model="cardPin"
                @blur="validateField('pin')"
                @input="validateField('pin', true)"
              />
              <div class="field--error">{{ pinError }}</div>
            </div>
            <div class="field">
              <button
                class="btn btn-primary"
                @click="submit"
                :disabled="isProcessing || isError"
              >
                Pay BHD{{ amount }}
              </button>
            </div>
          </div>

          <div class="benefit">
            <div class="benefit--text">
              This payment will be processed by
              <div class="large">The BENEFIT Company B.S.C</div>
            </div>
            <div class="benefit--img">
              <img src="~/static/images/benefit.svg" alt="BENEFIT" />
            </div>
          </div>

          <div class="secure">
            <img
              src="~/static/images/icon-lock.svg"
              alt="Connection encrypted"
            />
            Secured using 256 bit SSL encryption
          </div>
        </div>
        <div v-else key="result" class="container success">
          <svg
            version="1.1"
            xmlns="http://www.w3.org/2000/svg"
            viewBox="0 0 130.2 130.2"
          >
            <circle
              class="path circle"
              fill="none"
              stroke="#FFFFFF"
              stroke-width="6"
              stroke-miterlimit="10"
              cx="65.1"
              cy="65.1"
              r="62.1"
            />
            <polyline
              class="path check"
              fill="none"
              stroke="#FFFFFF"
              stroke-width="6"
              stroke-linecap="round"
              stroke-miterlimit="10"
              points="100.2,40.2 51.5,88.8 29.8,67.5 "
            />
          </svg>
        </div>
      </transition>
    </div>
    <div class="backdrop"></div>
  </div>
</template>

<script>
import Postmate from "postmate";
import MaskedInput from "../components/masked-input";

let encryptSuite; // populated by initEncryptionSuite() on mount

export default {
  data() {
    return {
      isOpen: false,
      handshakeComplete: false,
      // Form params
      key: null,
      amount: null,
      cardNumber: null,
      cardExpiry: null,
      cardPin: null,
      transactionId: null,
      title: "",
      subtitle: "",
      // State flags, for styling
      isProcessing: false, // set after "Pay" button is clicked
      isSuccess: false,
      // Error state handling
      keyError: null,
      numberError: null,
      expiryError: null,
      pinError: null,
    };
  },
  created() {
    if (process.client) {
      const handshake = new Postmate.Model({
        init: ({ key, amount, transactionId, title, subtitle }) => {
          this.handshakeComplete = true;
          this.key = key;
          this.amount = amount;
          this.transactionId = transactionId;
          this.title = title;
          this.subtitle = subtitle;
        },
        open: () => {
          this.isOpen = true;
        },
        reset: () => {
          // Hide and reset our state for the next potential run
          this.close();
          this.$router.go();
        },
      });

      handshake.then((parent) => {
        this.parent = parent;
      });
    }
  },
  mounted() {
    this.initEncryptSuite();
    this._focusOn("number");
  },
  methods: {
    initEncryptSuite() {
      // Uses asymmetric public key encryption with hard-coded certificate.
      // The private key is generated on the backend gateway and is never 
      // transmitted outside the hardened server.
      const lib = require("~/plugins/jsencrypt.client");
      const publicKey = 
        '-----BEGIN PUBLIC KEY-----' +
        'MIGfMA0GCSqGSIb3DQEBAQUAA4GNADCBiQKBgQCvtMA2cFzn8HZ8w85dKcPAu7d1' +
        'bDawqVvj9wwx9EyGWfSDtSn7wPMzPAXQGdUpKCG2qqnlklhSFxLfK4jW7c1b84BJ' +
        'RUjRiAnekRj684wg9ESV+NcTMOlWn51QWvmosXZ+2qzzAsfOit2DCzCp/pYdd+AD' +
        'FtRAWsv2EHgD0OZAVwIDAQAB' +
        '-----END PUBLIC KEY-----'

      encryptSuite = new lib.JSEncrypt()
      encryptSuite.setPublicKey(publicKey)
    },
    validateField(field, clearOnly = false) {
      let valid = false;
      let error = "Invalid field";

      switch (field) {
        case "number":
          valid = /^\d{16}$/.test(this.cardNumber);
          error = "Invalid card number";
          break;
        case "expiry":
          valid = /^(1[0-2]|0[1-9])(19|[2-9][0-9])$/.test(this.cardExpiry);
          error = "Invalid expiry";
          break;
        case "pin":
          valid = /^\d{4,}$/.test(this.cardPin);
          error = "Invalid PIN";
          break;
      }

      if (valid) {
        this[`${field}Error`] = null;
      } else if (!clearOnly) {
        // we don't display any errors in clearOnly mode
        this[`${field}Error`] = error;
      }
    },
    onCancel() {
      this.close();

      this.parent.emit("cancel");
    },
    onComplete() {
      this.close();
      this.parent.emit("complete"); // TODO: Return payment ID
    },
    close() {
      this.isOpen = false;
      setTimeout(() => this.parent.emit("close"), 1000); // animation out
    },
    async submit() {
      this.isProcessing = true;

      this.$axios.setHeader("Public-Key", this.key);
      this.$axios
        .$post("/pay", this._encryptedFields())
        .then((_) => {
          this.isSuccess = true;
          setTimeout(() => this.onComplete(), 1750); // animation out
        })
        .catch((error) => {
          if (error.response) {
            // The request was made and the server responded with a status code
            // that was outside the 2xx range
            this._onError(error.response.data);
          } else if (error.request) {
            // The request was made but no response was received
            this._onError(
              "The request timed out. Please check your internet connection and try again"
            );
          } else {
            // Something happened in setting up the request that triggered an Error
            alert(error.message);
          }
          // `error.config` contains the original request sent
        })
        .finally(() => {
          this.isProcessing = false;
        });
    },
    _onError(body) {
      let param = body.param || "number";
      this[`${param}Error`] = body.message;
      this._focusOn(param);
    },
    _focusOn(param) {
      // Moves cursor focus to the "param" input field
      switch (param) {
        case "pin":
          this.$refs[param].focus();
          break;
        case "number":
        case "expiry":
          // $el required for custom component <MaskedInput>
          this.$refs[param].$el.focus();
          break;
      }
    },
    _encryptedFields() {
      let month = parseInt(this.cardExpiry.substr(0, 2));
      let year = 2000 + parseInt(this.cardExpiry.substr(2, 2));
      
      return {
        transaction_id: this.transactionId,
        amount: this.amount,
        name: "Ahmed", // unused
        
        // encrypt all sensitive card details
        expiry_month: this.encrypt(`${month}`),
        expiry_year: this.encrypt(`${year}`),
        card_number: this.encrypt(this.cardNumber),
        pin: this.encrypt(this.cardPin),
      };
    },
    encrypt(str) {
      return encryptSuite.encrypt(str)
    }
  },
  computed: {
    isError() {
      return (
        this.keyError || this.numberError || this.expiryError || this.pinError
      );
    },
  },
  components: {
    MaskedInput,
  },
};
</script>

<style lang="scss" scoped>
html,
body {
  height: 100%;
}

.dialog {
  position: absolute;
  z-index: 100;
  width: 337px;
  height: 595px;
  margin: 0 auto;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -45%) scale(0.95);
  opacity: 0;
  /* applies when moving OUT of open state */
  transition: all 0.2s linear;
}
#modal.open .dialog {
  opacity: 1;
  transform: translate(-50%, -50%);
  /* applies when moving IN to open state */
  transition: all 0.4s cubic-bezier(0.76, -0.54, 0.49, 1.73) 0.25s;
}

.backdrop {
  background: radial-gradient(
    circle at center,
    rgba(0, 0, 0, 0.6) 0,
    rgba(0, 0, 0, 0.8) 100%
  );
  z-index: 99;
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  opacity: 0;
  pointer-events: none;
  /* applies when moving OUT of open state */
  transition: all 0.2s linear 0.2s;
}
#modal.open .backdrop {
  visibility: visible;
  opacity: 1;
  /* applies when moving IN to open state */
  transition: all 0.2s linear 0s;
  pointer-events: auto;
}

.container {
  width: 100%;
  height: 100%;
  display: flex;
  flex-direction: column;
  justify-content: normal;
  background: #fff;
  border-radius: 10px;
}
.headboard {
  height: 126px;
  position: relative;
  display: flex;
  flex-direction: column;
  justify-content: center;

  background: url("~@/static/images/papyrus.png") repeat top left;
  text-align: center;
  border-radius: 10px 10px 0 0;
  border-bottom: 1px solid #eee;

  .item--title {
    font-size: 28px;
  }
  .item--subtitle {
    padding-top: 10px;
    font-size: 16px;
    color: #999;
  }
  .close {
    position: absolute;
    top: 10px;
    right: 15px;
    cursor: pointer;
  }
}

.main,
.benefit {
  padding: 0 22px;
}

.main {
  display: flex;
  flex-direction: row;
  flex-wrap: wrap;
  margin-top: 25px;

  .field {
    width: 100%;
    margin-bottom: 20px;
  }

  .field--half {
    flex: 0 50%;

    &:nth-child(even) {
      padding-right: 5px;
    }
    &:nth-child(odd) {
      padding-left: 5px;
    }
  }

  .field--error {
    color: #b45054;
    position: relative;
    left: 10px;
    top: 7px;
    max-height: 0; /* We animate max-height (since you can't animate height: auto) */
    /* 
      To animate out of error state, we would need to avoid clearing the error text since
      that will immediately collapse the element to zero-height. To do this, we could have
      and error flag separate from the error text.
      - Only "set" error text, without ever clearing it
      - Use error flag for showing/hiding error text
     */
  }

  .field.error .field--error {
    max-height: 3em;
    transition: max-height 1s cubic-bezier(0.17, 0.67, 0.83, 0.67); // animate-in to error
  }
}

form {
  width: 100%;
}

.btn {
  height: 54px;
  width: 100%;
  display: block;
  margin: 20px 0 30px;
  font-size: 20px;
  background: linear-gradient(to bottom, #ec2029, #d73239);
  border: 0;
  font-family: "OpenSans-Bold", "Helvetica Neue", Arial, sans-serif;
  color: #fff;
  border-radius: 4px;
  text-shadow: 0px 1px 1px rgba(0, 0, 0, 0.25);
  box-shadow: inset 0px 1px 1px #ec2029, inset 0px 2px 1px #ffffff;
  cursor: pointer;

  &:disabled {
    background: #999;
    box-shadow: inset 0 1px 1px #777;
    text-shadow: none;
    color: #ccc;
  }
}

label {
  text-transform: uppercase;
  font-size: 16px;
  display: block;
  line-height: 42px;
  margin-left: 10px;

  &[for="card-pin"] img {
    position: relative;
    top: 4px;
    cursor: pointer;
  }
}

.field input {
  height: 51px;
  width: 100%;
  padding: 3px 10px;
  border-radius: 4px;
  border: 1px solid #aaa;
  background: linear-gradient(to bottom, #fff, #f3f3f3);
  box-shadow: inset 0 2px 4px rgba(0, 0, 0, 0.25);
  font-size: 18px;
  color: #666;
  letter-spacing: 2px;
}

.field.error input {
  background: linear-gradient(to bottom, #fef6f7, #f9e6e9);
  border: 2px solid #b45054;
  color: #b45054;
  box-shadow: 0 0 3px 1px rgba(200, 0, 0, 0.3);
}

.benefit {
  display: flex;
  flex-flow: row nowrap;
  width: 100%;
}
.benefit--text {
  padding-top: 5px;
  font-size: 12px;
  width: 100%;
  line-height: 20px;

  .large {
    font-size: 16px;
    line-height: 34px;
  }
}
.benefit--img {
  width: 55px;
}

.secure {
  height: 36px;
  line-height: 36px;
  background: url("~@/static/images/papyrus.png") repeat top left;
  text-align: center;
  font-size: 13px;
  border-radius: 0 0 10px 10px;

  img {
    position: relative;
    top: 4px;
    left: -2px;
  }
}

.success {
  background: #73af55;
  justify-content: space-around;

  svg {
    width: 150px;
    display: block;
    margin: 0 auto;
  }

  .path {
    stroke-dasharray: 1000;
    stroke-dashoffset: 0;

    &.circle {
      animation: dash 0.9s ease-in-out;
    }

    &.check {
      stroke-dashoffset: -100;
      animation: dash-check 0.9s 0.35s ease-in-out forwards;
    }
  }

  @keyframes dash {
    0% {
      stroke-dashoffset: 1000;
    }
    100% {
      stroke-dashoffset: 0;
    }
  }

  @keyframes dash-check {
    0% {
      stroke-dashoffset: -100;
    }
    100% {
      stroke-dashoffset: 900;
    }
  }
}

.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.3s ease-in-out;
}

.fade-enter,
.fade-leave-to {
  opacity: 0.4;
}
</style>
