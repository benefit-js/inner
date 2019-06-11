<template>
  <div>
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
          <div class="label">Debit Card Number</div>
          <input type="tel" name="number" pattern="[0-9]{16}" placeholder="•••• •••• •••• ••••">
        </div>
        <div class="field">
          <div class="label">Expiry Date</div>
          <input type="tel" name="number" pattern="[0-9]{1-2}/[0-9]{2}" placeholder="MM/YY">
        </div>
        <div class="field">
          <div class="label">ATM PIN</div>
          <input type="tel" name="number" pattern="[0-9]{4}" placeholder="••••">
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
