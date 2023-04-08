[![Build Status](https://img.shields.io/travis/telegraf/telegraf-i18n.svg?branch=master&style=flat-square)](https://travis-ci.org/telegraf/telegraf-i18n)
[![NPM Version](https://img.shields.io/npm/v/telegraf-i18n.svg?style=flat-square)](https://www.npmjs.com/package/telegraf-i18n)
[![js-standard-style](https://img.shields.io/badge/code%20style-standard-brightgreen.svg?style=flat-square)](http://standardjs.com/)

# i18n for Telegraf

Internationalization middleware for [Telegraf](https://www.npmjs.com/package/telegraf).

## Installation

```js
$ npm i i18n-telegraf
```

# !!!In the current version, it only works on yaml and has an error on json

## Example

```js
import { Telegraf, session } from "telegraf";
import TelegrafI18n from "i18n-telegraf";

/* 
yaml are ok
Example directory structure:
├── locales
│   ├── en.yaml
│   ├── en-US.yaml
│   └── ru.yaml
└── bot.js
*/
const app = new Telegraf(process.env.BOT_TOKEN);

//
const i18n = new TelegrafI18n({
  defaultLanguage: "en",
  allowMissing: false, // Default true
  directory: path.resolve(__dirname, "locales"),
});

// Also you can provide i18n data directly
i18n.loadLocale("en", { greeting: "Hello!" });

app.use(i18n.middleware());

app.hears("/start", (ctx) => {
  const message = ctx.i18n.t("greeting");
  return ctx.reply(message);
});
```

See full [example](/examples).

## User context

Telegraf user context props and functions:

```js
app.use((ctx) => {
  ctx.i18n.locale(); // Get current locale
  ctx.i18n.locale(code); // Set current locale
  ctx.i18n.t(resourceKey, [context]); // Get resource value (context will be used by template engine)
});
```

## Helpers

```js
const { match, reply } = require('telegraf-i18n')

// In case you use custom keyboard with localized labels.
bot.hears(match('keyboard.foo'), (ctx) => ...)

//Reply helper
bot.command('help', reply('help'))
```
