# Logs — 3 test scenarios

## Scenario 1: Successful calculation (12 + 7)

20 Sep 12:30:29 - [warn] [function:Calc] [2026-09-20T09:30:29.126Z] chatId=1387663979 step=calc:input_received :: raw="12 + 7"
20 Sep 12:30:29 - [warn] [function:Calc] [2026-09-20T09:30:29.126Z] chatId=1387663979 step=calc:success :: 12 + 7 = 19

User sent "12 + 7" to the calculator branch. The bot logged `calc:input_received` with the raw input, then validated and computed the result, logging `calc:success` with the computed value. The user saw "✅ Результат: 12 + 7 = 19" in Telegram, with no delay or hang.

## Scenario 2: Invalid input (abc)

20 Sep 12:30:45 - [warn] [function:Calc] [2026-09-20T09:30:45.816Z] chatId=1387663979 step=calc:input_received :: raw="abc"
20 Sep 12:30:45 - [warn] [function:Calc] [2026-09-20T09:30:45.817Z] chatId=1387663979 step=calc:validation_error :: unrecognized format

User sent "abc" instead of a valid expression. The regex failed to match the number-operator-number pattern, so the bot logged `calc:validation_error` with reason "unrecognized format" instead of crashing or hanging. The user saw a friendly message ("⚠️ Не розпізнав вираз...") asking them to use the correct format.

## Scenario 3: External service failure (broken NBU API URL)

20 Sep 12:32:53 - [warn] [function:menu func] [2026-09-20T09:32:53.675Z] chatId=1387663979 step=nbu:request_sent :: calling NBU API
20 Sep 12:32:53 - [warn] [function:Currency Handler] [2026-09-20T09:32:53.753Z] chatId=1387663979 step=nbu:response_received :: statusCode=401
20 Sep 12:32:53 - [warn] [function:Currency Handler] [2026-09-20T09:32:53.753Z] chatId=1387663979 step=nbu:error :: invalid response or USD/EUR missing

The NBU API URL was intentionally broken to simulate a service outage. `nbu:request_sent` was logged right before the call; the broken endpoint returned `statusCode=401` instead of the expected currency array, which failed the `Array.isArray`/USD-EUR check and was logged as `nbu:error`. The user saw "⚠️ Не вдалося отримати курс, спробуйте пізніше" instead of a raw error, a stack trace, or a hang.
