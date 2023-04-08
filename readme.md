# i18n for Telegraf

Internationalization middleware for [Telegraf](https://www.npmjs.com/package/telegraf).

# !!! In the current version, it only works on yaml and has an error on json

## Installation

```js
$ npm i i18n-telegraf
```

## Example

Structure

```js
/* 
yaml are ok
Example directory structure:
├── locales
│   ├── en.yaml
│   ├── en-US.yaml
│   └── fa.yaml
└── bot.js
*/
```

en.yaml :

```js
welcome: Welcome To Bot
```

bot.js

```js
import { Telegraf } from "telegraf";
import TelegrafI18n from "i18n-telegraf";

const app = new Telegraf(process.env.BOT_TOKEN);

const i18n = new TelegrafI18n({
  defaultLanguage: "en",
  allowMissing: false, // Default true
  directory: path.resolve(__dirname, "locales"),
});

app.use(i18n.middleware());

app.hears("/start", (ctx) => {
  const message = ctx.i18n.t("welcome");
  return ctx.reply(message);
});
```

## Example with Session

Session used : https://www.npmjs.com/package/@telegraf/session

```js
import { Telegraf, session } from "telegraf";
import TelegrafI18n from "i18n-telegraf";
// session
import { MySQL } from "@telegraf/session/mysql";

const app = new Telegraf(process.env.BOT_TOKEN);

const store = MySQL({
  host: "localhost",
  database: process.env.USER_DATA,
  user: process.env.NAME_DATA,
  password: process.env.PASSWORD_DATA,
});

app.use(session({ store, defaultSession: () => ({ __language_code: "en" }) }));
// __language_code It must be set for the language in all sessions

const i18n = new TelegrafI18n({
  directory: "locales",
  sessionName: "session",
  useSession: true,
});

app.use(i18n.middleware());

app.hears("/start", (ctx) => {
  const message = ctx.i18n.t("welcome");
  return ctx.reply(message);
});
```

## Change Locales

How to change the language

```js
app.hears("/locales_en", (ctx) => {
  ctx.i18n.locale("en");
  return ctx.reply("Changes Succesfuly");
});

app.hears("/locales_fa", (ctx) => {
  ctx.i18n.locale("fa");
  return ctx.reply("با موفقیت تغییر کرد");
});
```

## Get Locales

Get the language

```js
app.hears("/get_locales", (ctx) => {
  const locales = ctx.i18n.locale(); // Get current locale
  return ctx.reply(`Your language is fa ${locales}`);
});
```

## Change part of the text

If you have a text that needs to be changed, use the section below

en.yaml :

```js
welcome: Welcome ${name} To Bot
test : has a ${test}
```

```js
app.hears("/start", (ctx) => {
  const message = ctx.i18n.t("welcome", { name: ctx.message.first_name });
  return ctx.reply(message);
});

app.hears("/test", (ctx) => {
  const message = ctx.i18n.t("test", { test: "Any text" });
  return ctx.reply(message);
});
```
