# QED Currency Calculator

## Install

```bash
npm i --save qed-currencies-calculator
```
## Preview

![Component Preview](https://raw.githubusercontent.com/QEDteam/qed-currencies-calculator/master/dist/calculator.png)
```javascript
    import QedCurrencyCalculator from 'qed-currencies-calculator'
    import 'qed-currencies-calculator/dist/qed-currency-calculator-style.css'
```
More styles will be available soon.

## Usage

Import component

```javascript
    import QedCurrencyCalculator from 'qed-currencies-calculator'
    import 'qed-currencies-calculator/dist/qed-currency-calculator-style.css'

    const app = new Vue({
        components: {
            QedCurrencyCalculator,
        }
    };
```

Allowed currencies

```javascript
    currencies: {
        'EUR',
        'USD',
        'JPY',
        'GBP',
        'BGN',
        'CZK',
        'DKK',
        'HUF',
        'PLN',
        'RON',
        'SEK',
        'CHF',
        'ISK',
        'NOK',
        'HRK',
        'RUB',
        'TRY',
        'AUD',
        'BRL',
        'CNY',
        'HKD',
        'IDR',
        'ILS',
        'INR',
        'KRW',
        'MXN',
        'MYR',
        'NZD',
        'PHP',
        'SGD',
        'THB',
        'ZAR',
    }
```

```html
<div>
    <qed-currency-calculator/>
</div>
```

## License

[MIT](http://vjpr.mit-license.org)

[npm-image]: https://img.shields.io/npm/v/live-xxx.svg
[npm-url]: https://npmjs.org/package/live-xxx
[travis-image]: https://img.shields.io/travis/live-js/live-xxx/master.svg
[travis-url]: https://travis-ci.org/live-js/live-xxx
[coveralls-image]: https://img.shields.io/coveralls/live-js/live-xxx/master.svg
[coveralls-url]: https://coveralls.io/r/live-js/live-xxx?branch=master
