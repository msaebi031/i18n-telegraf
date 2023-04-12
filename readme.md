# i18n for Telegraf

Internationalization middleware for [Telegraf](https://www.npmjs.com/package/telegraf).

## Installation

```js
$ npm i i18n-telegraf
```

## Example

Structure

```js
/* 
yaml or json are ok
Example directory structure:
├── locales
│   ├── en.yaml
│   ├── en-US.yaml
│   └── fa.yaml
└── bot.js

or

├── locales
│   ├── en.json
│   ├── en-US.json
│   └── fa.json
└── bot.js
*/
```

en.yaml :

```yaml
welcome: Welcome To Bot
```

or

en.json :

```json
{ "welcome": "Welcome To Bot" }
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

```yaml
welcome: Welcome ${name} To Bot
test: has a ${test}
```

or

en.json :

```json
{
  "welcome": "Welcome ${name} To Bot",
  "test": "has a ${test}"
}
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

## Reading items in another language

You can read other languages ​​without setting the language for your locale.

```js
const Midi18n = new TelegrafI18n({
  defaultLanguage: "en",
  allowMissing: false, // Default true
  directory: path.resolve(__dirname, "locales"),
});

app.use(Midi18n.middleware());

app.hears("/start", (ctx) => {
  // You must use the variable you used for the middleware option
  // Midi18n.t(local,key,{});
  const message = Midi18n.t("fa", "welcome");
  return ctx.reply(message);
});
```
