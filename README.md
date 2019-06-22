# Inner [![Netlify Status](https://api.netlify.com/api/v1/badges/2690825e-a238-45de-9cf4-5749165dc1a3/deploy-status)](https://app.netlify.com/sites/benefitjs-checkout/deploys)

This is the actual Checkout screen for the [benefit.js](https://github.com/benefit-js/benefit-js) library.

## TODO

- [ ] Add input masking for card number, expiry date using [imask](https://github.com/uNmAnNeR/imaskjs/tree/master/packages/vue-imask) or [your own implementation](https://stackoverflow.com/a/55010378/2022751)
- [ ] Add responsive styling for card form (full width on mobile, without border-radius)
- [ ] Add field-specific error messages, allowing for highlighting erroneous fields when an error is encountered
- [ ] Add CSS style transitions for error/success states

## Build Setup

``` bash
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
