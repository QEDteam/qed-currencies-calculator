<template>
    <div class="qed-container">
        <div v-if="checkSetup">
            <input type="text" class="qed-input" v-model="input">

            <div class="value">{{ this.result }}</div>
            <select class="qed-select" v-model="resultCurrency">
                <option v-for="(curreny, key) in currencies" v-bind:key="key" :value="key">{{ key }}</option>
            </select>
        </div>
        
        <div class="loader" v-else><div></div><div></div><div></div><div></div><div></div><div></div><div></div><div></div></div>
    </div>
</template>

<script>
import axios from "axios";

export default {
    name: "QedCurrenyCalculator",
    props: {
        msg: String
    },
    data() {
        return {
            input: "",
            rates: {},
            defaultKey: "EUR",
            resultCurrency: "EUR",
            currencies: {
                EUR: {
                    symbols: ["€", "eur", "euro", "euros"]
                },
                USD: {
                    symbols: ["$", "usd", "dollar", "american dollar", "dollars"]
                },
                JPY: {
                    symbols: ["¥", "jpy", "yen", "japanese yen", "JP¥"]
                },
                GBP: {
                    symbols: ["£", "gbp", "pounds", "sterling", "pound"]
                },
                BGN: {
                    symbols: ["bgn", "lev", "levs", "лв", "лев", "leva", "левове"]
                },
                CZK: {
                    symbols: ["koruna", "czk", "Kč", "Czech krone", "Czech crown"]
                },
                DKK: {
                    symbols: ["krone", "dkk", "kroner", "kr."],
                },
                HUF: {
                    symbols: ["forint", "Ft", "huf"],
                },
                PLN: {
                    symbols: ["złoty", "zł", "zloty", "zlote", "pln"],
                },
                RON: {
                    symbols: ["leu", "lei", "L", "lew", "ron"],
                },
                SEK: {
                    symbols: ["kr", "sek", "krona", "kronor", "spänn", "pix"],
                },
                CHF: {
                    symbols: ["franc", "chf", "franken", "Fr.", "SFr.", "francs"],
                },
                ISK: {
                    symbols: ["isk"]
                },
                NOK: {
                    symbols: ["nok"]
                },
                HRK: {
                    symbols: ["hrk", "kuna", "kn"]
                },
                RUB: {
                    symbols: ["ruble", "rouble", "rubli", "rub", "₽", "рубль", "рубли́", "руб"],
                },
                TRY: {
                    symbols: ["try", "tl", "₺", "lira"]
                },
                AUD: {
                    symbols: ["aud"]
                },
                BRL: {
                    symbols: ["brl", "real", "R$", "reais"]
                },
                CNY: {
                    symbols: ["cny", "Renminbi", "yuan"]
                },
                HKD: {
                    symbols: ["hkd", "港幣", "HK$"]
                },
                IDR: {
                    symbols: ["idr", "Rp", "rupiah", "perak"]
                },
                ILS: {
                    symbols: ["ils", "shekel", "shekels", "sheqalim"]
                },
                INR: {
                    symbols: ["inr", "Rupee", "₹"]
                },
                KRW: {
                    symbols: ["krw", "₩"]
                },
                MXN: {
                    symbols: ["mxn", "peso"]
                },
                MYR: {
                    symbols: ["myr"]
                },
                NZD: {
                    symbols: ["nzd"]
                },
                PHP: {
                    symbols: ["php", "₱"]
                },
                SGD: {
                    symbols: ["sgd"]
                },
                THB: {
                    symbols: ["thb", "฿", "baht"]
                },
                ZAR: {
                    symbols: ["zar", "rand"]
                },
            }
        };
    },
    computed: {
        /**
         * Check whether setup is finished
         */
        checkSetup() {
            for (const key in this.currencies) {
                if (this.rates[key] == null) {
                    return false;
                }
            }
            return true;
        },

        /**
         * Parse user input and create tokens
         */
        tokens() {
            const regex = /(?=\+)|(?=-)|(?=\*)|(?=\/)/g;

            let tokens = this.input.replace(/\s/g, "").split(regex);
            let parsedTokens = tokens
                .map(token => {
                    const operator = token.match(/(\+)|(-)|(\*)|(\/)/g);
                    const number = token.match(/^\d+|\d+\b|\d+(?=\w)/g);
                    const currency = token.split(/^\d+|\d+\b|\d+(?=\w)/g)[1];

                    if (number == null) {
                        return;
                    }

                    return {
                        operator: operator ? operator[0] : "+",
                        value: number[0],
                        currency: currency
                    };
                })
                .filter(Boolean);

            let copy = parsedTokens;
            let prev = this.defaultKey;
            copy.forEach((element, index) => {
                let validCurrency = this.getValidCurrency(element.currency);
                if (validCurrency !== null) {
                    prev = validCurrency;
                }

                if (index === 0) {
                    this.resultCurrency = prev;
                }

                parsedTokens[index].currency = prev;
            });

            return parsedTokens;
        },

        /**
         * Calculate result
         */
        result() {
            let res = 0;
            let firstCurrency = this.tokens[0]
                ? this.tokens[0].currency
                : this.defaultKey;

            let goalCurrency = this.resultCurrency
                ? this.resultCurrency
                : firstCurrency;

            let ratesCopy = JSON.parse(JSON.stringify(this.rates));

            for (const key in ratesCopy) {
                ratesCopy[key] /= ratesCopy[goalCurrency];
            }
            this.tokens.forEach((token, index) => {
                // First element always +
                if (index === 0) {
                    res += Number(token.value) / ratesCopy[token.currency];
                    return;
                }

                let converted = Number(token.value) / ratesCopy[token.currency];
                res = eval(res + token.operator + converted);
            });
            return res.toFixed(2);
        }
    },
    mounted() {
        this.getRates();
    },
    methods: {
        /**
         * Get currency equivalent
         */
        getValidCurrency(value) {
            for (const key in this.currencies) {
                let matchingCurrency = this.currencies[key].symbols.find(
                    curr => {
                        return curr.toLowerCase() == value.toLowerCase();
                    }
                );

                if (matchingCurrency != null) {
                    return key;
                }
            }

            return null;
        },

        /**
         * Default exchange rate service provider
         */
        getRates() {
            axios
                .get(
                    `https://exchange.qed.services/api/v1/get-rate?from=${
                        this.defaultKey
                    }`
                )
                .then(response => {
                    this.rates = response.data.rates;
                })
                .catch(() => {
                    this.rates = {};
                    setTimeout(() => {
                        this.getRates();
                    }, 3000);
                });
        }
    }
};
</script>