# Inner [![Netlify Status](https://api.netlify.com/api/v1/badges/2690825e-a238-45de-9cf4-5749165dc1a3/deploy-status)](https://app.netlify.com/sites/benefitjs-checkout/deploys)

This is the actual Checkout screen for the [benefit.js](https://github.com/benefit-js/benefit-js) library.

## TODO

- [x] Add input masking for card number, expiry date using [imask](https://github.com/uNmAnNeR/imaskjs/tree/master/packages/vue-imask) or [your own implementation](https://stackoverflow.com/a/55010378/2022751)
- [x] Refactor month/year validation for expiry date (it's currently being done twice in `validateField` and in `getDateParts`)
- [x] Add field-specific error messages, allowing for highlighting erroneous fields when an error is encountered
- [x] Add max-length to fields
- [x] Autofocus on error field (from server submission)
- [x] Use number pad for PIN field
- Fix error UX
  - [ ] Clear errors as soon as validation passes (on key press)
  - [ ] Display errors only on blur (without clear/set existing value)
- [ ] Add CSS style transitions for error/success states
- [ ] Add responsive styling for card form (full width on mobile, without border-radius)
- [ ] Check CGN on modal load & display error before form submission
- [ ] Add progress indicator for "Pay" button while processing

## Build Setup

```bash
# install dependencies
$ yarn install

# serve with hot reload at localhost:3000
$ yarn run dev

# build for production and launch server
$ yarn run build
$ yarn start

# generate static project
$ yarn run generate
```

For detailed explanation on how things work, checkout [Nuxt.js docs](https://nuxtjs.org).
