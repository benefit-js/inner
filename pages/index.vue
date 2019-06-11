<template>
  <div class="container">
    <div class="headboard">
      <div class="merchant--name">Your Company</div>
      <div class="merchant--url">https://yourcompany.bh</div>
      <div class="close">
        <img src="~/static/images/close.svg" alt="Close modal">
      </div>
    </div>
    <div class="main">
      <form action="/">
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
        <div class="half-field">
          <label>Expiry Date</label>
          <input
            id="card-expiry"
            type="tel"
            name="number"
            pattern="[0-9]{1-2}/[0-9]{2}"
            placeholder="MM/YY"
          >
        </div>
        <div class="half-field">
          <label for="card-pin">ATM PIN</label>
          <input id="card-pin" type="tel" name="number" pattern="[0-9]{4}" placeholder="••••">
        </div>
        <button class="btn btn-primary">Pay BHD12.345</button>
      </form>

      <div class="benefit">
        This payment will be processed by
        The BENEFIT Company B.S.C
        <img
          src="~/static/images/benefit.svg"
          alt="BENEFIT"
        >
      </div>

      <div class="secure">
        <img src="~/static/images/icon-lock.svg" alt="Connection encrypted">
        Secured using 256 bit SSL encryption
      </div>
    </div>
  </div>
</template>

<style lang="scss" scoped>
.container {
  width: 100%;
  height: 100%;
}
.headboard {
  background: url("~@/static/images/papyrus.png") repeat top left;
  position: relative;
  height: 126px;
  text-align: center;
  padding-top: 36px;
  border-radius: 10px 10px 0 0;
  border-bottom: 1px solid #eee;

  .merchant--name {
    font-size: 28px;
    color: #666;
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
  }
}

label {
  text-transform: uppercase;
  font-size: 16px;
  color: #666;
  display: block;
}
.field {
  margin: 0 auto;
  width: 100%;
}
.half-field {
  width: 50%;
  display: inline-block;
}
%textbox {
  height: 51px;
  padding: 3px 6px;
  border-radius: 4px;
  border: 1px solid #aaa;
  background: linear-gradient(to bottom, #fff, #f3f3f3);
  box-shadow: inset 0 2px 4px rgba(0, 0, 0, 0.25);
  font-size: 22px;
}
#card-number {
  @extend %textbox;
  width: 100%;
}
#card-expiry,
#card-pin {
  @extend %textbox;
  width: 50%;
}
</style>
