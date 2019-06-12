<template>
  <div class="container">
    <div class="headboard">
      <div class="merchant--name">Your Company</div>
      <div class="merchant--url">https://yourcompany.bh</div>
      <div class="close">
        <img src="~/static/images/close.svg" alt="Close modal" @click="onCancel">
      </div>
    </div>
    <div class="main">
      <div class="field">
        <label for="card-number">Debit Card Number</label>
        <input
          id="card-number"
          type="tel"
          name="number"
          pattern="[0-9]{16}"
          placeholder="•••• •••• •••• ••••"
        >
      </div>
      <div class="field--half">
        <label>Expiry Date</label>
        <input
          id="card-expiry"
          type="tel"
          name="number"
          pattern="[0-9]{1-2}/[0-9]{2}"
          placeholder="MM/YY"
        >
      </div>
      <div class="field--half">
        <label for="card-pin">
          ATM PIN
          <img src="~/static/images/question.svg">
        </label>
        <input id="card-pin" type="password" name="number" pattern="[0-9]{4}" placeholder="••••">
      </div>
      <div class="field">
        <button class="btn btn-primary">Pay BHD12.345</button>
      </div>
    </div>

    <div class="benefit">
      <div class="benefit--text">
        This payment will be processed by
        <div class="large">The BENEFIT Company B.S.C</div>
      </div>
      <div class="benefit--img">
        <img src="~/static/images/benefit.svg" alt="BENEFIT">
      </div>
    </div>

    <div class="secure">
      <img src="~/static/images/icon-lock.svg" alt="Connection encrypted">
      Secured using 256 bit SSL encryption
    </div>
  </div>
</template>

<script>
import Postmate from "postmate";

export default {
  data() {
    return {
      isOpen: false,
      handshakeComplete: false
    };
  },
  created() {
    if (process.client) {
      const handshake = new Postmate.Model({
        init: ({ publicKey, amount, transactionId }) => {
          this.handshakeComplete = true;
          this.publicKey = publicKey;
          this.amount = amount;
          alert(`amount = ${this.amount}`);
        },
        open: () => {
          this.isOpen = true;
        },
        reset: () => {
          // Hide and reset our state for the next potential run
          this.close();
          this.$router.go();
        }
      });

      handshake.then(parent => {
        this.parent = parent;
      });
    }
  },
  methods: {
    onCancel() {
      this.close();

      this.parent.emit("cancel");
    },
    onComplete() {
      // TODO: Return payment result
      this.close();

      this.parent.emit("complete");
    },
    close() {
      // TODO: CLOSE the dialog
      this.parent.emit("close");
    }
  }
};
</script>

<style lang="scss" scoped>
html,
body {
  height: 100%;
}

.container {
  width: 100%;
  height: 100%;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  background: #fff;
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

  .merchant--name {
    font-size: 28px;
  }
  .merchant--url {
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

  .field,
  .field--half {
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

#card-number,
#card-expiry,
#card-pin {
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

.benefit {
  display: flex;
  flex-flow: row nowrap;
  width: 100%;
}
.benefit--text {
  font-size: 12px;
  width: 100%;
  line-height: 24px;

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

  img {
    position: relative;
    top: 4px;
    left: -2px;
  }
}
</style>
