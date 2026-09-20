# NovaIT Test Task — Application Engineer (Telegram Bot on RedBot/Node-RED)

## How to run

1. Install Node.js (v18+), then:

npm install -g --unsafe-perm node-red

2. Start Node-RED once to create the config folder, then stop it (Ctrl+C):

node-red

3. Install RedBot nodes:

cd ~/.node-red
npm install node-red-contrib-chatbot

4. Start Node-RED again:

node-red

5. Open the editor at `http://localhost:1880`
6. Menu (☰) → Import → select `flows.json` from this repo
7. Double-click the **Telegram Receiver** node → edit the bot config → paste your own bot token (create one via `@BotFather` in Telegram) into the **YOUR_TELEGRAM_TOKEN** field
8. Deploy, then message your bot with `/start`

## What's implemented

- ✅ RedBot deployed locally, bot responds to `/start`
- ✅ Main menu with 3 inline buttons (Calculator, Currency, About) + `/menu` and `back` return to menu
- ✅ Fallback message for unrecognized input, always re-shows the menu
- ✅ Calculator: JS logic in a Function node (no built-in math node), supports `+ − * /` (and `× ÷` as aliases), works with or without spaces (`8*8` and `8 * 8`)
- ✅ Calculator validation: non-numeric input, division by zero, empty message, input over 50 chars
- ✅ Currency rate from NBU API (USD/EUR), formatted human-readable with date
- ✅ Graceful fallback message when the NBU API is unreachable or returns an error
- ✅ Structured logging (`node.warn`) at key steps: `calc:input_received`, `calc:success`, `calc:validation_error`, `nbu:request_sent`, `nbu:response_received`, `nbu:success`, `nbu:error` — each with timestamp, chatId, step name

## Known issues / not done

- Calculator accepts only non-negative (natural) numbers as operands — the task didn't specify negative-number handling, so this was scoped out to focus on the explicitly required validation cases
- NBU API sometimes returns the _next_ business day's rate — this is expected NBU publishing behavior (they publish the official rate a day ahead), not a bug; the bot displays whatever date the API returns
- Rebuilt routing as a single function node with explicit if/else after the switch node's "check all rules" mode double-fired branches
- NLP/free-text input (bonus) not attempted — scoped out given the 4-hour budget
- No automated tests (unit/integration) were written — validated manually by running all 3 required scenarios end-to-end via Telegram

## Time spent

3.5 hours

## Checklist

| Пункт                                       | Статус | Коментар                                          |
| ------------------------------------------- | ------ | ------------------------------------------------- |
| RedBot розгорнуто, бот відповідає на /start | ✅     |                                                   |
| Меню з 3 пунктів + кнопка «Назад»           | ✅     | реалізовано через `/menu` та `back`               |
| Fallback на нерозпізнаний ввід              | ✅     |                                                   |
| Калькулятор рахує коректно                  | ✅     | JS у Function-ноді, без готової арифметичної ноди |
| Валідація: нечислове значення               | ✅     |                                                   |
| Валідація: ділення на нуль                  | ✅     |                                                   |
| Валідація: порожній / задовгий ввід         | ✅     |                                                   |
| Курс валют з API НБУ                        | ✅     |                                                   |
| Обробка недоступності API                   | ✅     |                                                   |
| Логи: успішний сценарій                     | ✅     | див. logs.md                                      |
| Логи: некоректний ввід                      | ✅     | див. logs.md                                      |
| Логи: збій API                              | ✅     | див. logs.md                                      |
| README англійською                          | ✅     |                                                   |

## Bonus

- ✅ Network diagnostics: `ping bank.gov.ua` and `tracert bank.gov.ua` — see below
- ✅ Postman collection with 2-3 requests to NBU API — see `postman/`
- ❌ Free-text / NLP input — skipped due to time budget

### Network diagnostics

Pinging bank.gov.ua [172.65.90.64] with 32 bytes of data:
Reply from 172.65.90.64: bytes=32 time=10ms TTL=59
(4 packets sent, 0% loss, avg 10ms)

Tracing route to bank.gov.ua [172.65.90.64]:
1 <1 ms 192.168.0.1
2 2 ms 2-212-178-93.dsl.lviv.farlep.net [93.178.212.2]
3 4 ms 88.214.127.40
4 10 ms umc-10G-gw.ix.net.ua [185.1.50.50]
5 11 ms cloudflare-gw.ix.net.ua [185.1.50.68]
6 10 ms 172.65.90.64

6 hops, ~10ms average latency, 0% packet loss — bank.gov.ua sits behind Cloudflare (`cloudflare-gw.ix.net.ua`), reachable directly from a Lviv ISP without unusual routing detours.
