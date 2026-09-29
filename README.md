# v2rayN: правила для России под Xray TUN

**Русский** | [English](README.en.md)

Три набора правил маршрутизации для [v2rayN](https://github.com/2dust/v2rayN) в режиме **Xray TUN**.
Во всех наборах стратегия `IPIfNonMatch`. Правила опираются на geo-файлы
[runetfreedom](https://github.com/runetfreedom/russia-v2ray-rules-dat).
Готовые файлы лежат в [релизах](https://github.com/OushenView/v2rayn-ru-xraytun/releases).

| набор | в прокси | напрямую |
|---|---|---|
| Всё, кроме РФ (Whitelist) | всё зарубежное | российские домены и IP, локальная сеть, qBittorrent |
| Только заблокированное (Blacklist) | заблокированное в РФ (`ru-blocked`), Discord, DNS 1.1.1.1 и 8.8.8.8 | всё остальное, qBittorrent |
| Всё через прокси (Global) | всё, кроме локальной сети | локальная сеть |

Во всех наборах QUIC (UDP/443) заблокирован: браузеры переходят на TCP, где Xray видит имя сайта.

В v2rayN к имени добавляется версия: `V1-RU-Xtun-Всё, кроме РФ (Whitelist)`. `V1` — версия
наборов, она же номер релиза.

## Что нужно

- v2rayN с ядром Xray (проверено на 7.25.2).
- Включённый TUN. В «Настройки → Параметры → Настройки режима TUN» выключена
  «Устаревшая защита TUN (Legacy Protect)», иначе TUN будет от sing-box.
- Geo-файлы и DNS из пресета «Россия» — шаг 1 установки.

## Установка

> **Сначала пресет «Россия».** Правила используют категории `ru-blocked` и `category-ru`
> из geo-файлов runetfreedom. В стандартных geo-файлах v2rayN их нет, и без них ядро
> не запустится.

1. **Настройки → Настройка региональных пресетов → Россия.** v2rayN скачает geo-файлы
   и DNS runetfreedom. Заодно появятся их собственные наборы правил — их можно удалить.
2. **Настройки → Параметры → Настройки v2rayN → Источник правил маршрутизации.** Вставьте
   ссылку и сохраните:
   ```
   https://github.com/OushenView/v2rayn-ru-xraytun/releases/latest/download/template.json
   ```
3. **Настройки → Настройки маршрутизации → Импортировать правила.** Появятся три набора:
   `V1-RU-Xtun-Всё, кроме РФ (Whitelist)`, `V1-RU-Xtun-Только заблокированное (Blacklist)`
   и `V1-RU-Xtun-Всё через прокси (Global)`.
4. Выберите нужный набор в главном окне или в меню в трее.

Импортируйте при подключённом прокси: v2rayN скачивает правила через него.

Если ядро не запускается с ошибкой про `ru-blocked`, значит, geo-файлы остались стандартными.
Повторите шаг 1 или обновите GeoFiles через «Обновить».

Один набор можно поставить и без шаблона. В «Настройках маршрутизации» создайте набор,
выберите «Доменная стратегия» `IPIfNonMatch`, в поле «URL (необязательно)» вставьте ссылку
на файл из релиза и нажмите «Импорт правил из URL». Ссылки на файлы последнего релиза:

```
https://github.com/OushenView/v2rayn-ru-xraytun/releases/latest/download/whitelist.json
https://github.com/OushenView/v2rayn-ru-xraytun/releases/latest/download/blacklist.json
https://github.com/OushenView/v2rayn-ru-xraytun/releases/latest/download/global.json
```

## Обновление

Сам v2rayN наборы не обновляет. Новые версии выходят
[релизами](https://github.com/OushenView/v2rayn-ru-xraytun/releases). Обновиться можно двумя способами:

- «Импортировать правила» ещё раз. Новая версия придёт с новым номером в имени
  (`V2-RU-Xtun-…`), старые наборы удалите.
- Одним набором: откройте его, вставьте в «URL (необязательно)» ссылку на его файл из релиза,
  нажмите «Импорт правил из URL» и на вопрос «Хотите добавить правила?» ответьте «Нет» —
  правила заменятся.

## Под себя

### Свои программы

В правилах `Direct MY process` (Whitelist и Blacklist) и `Proxy MY process` (Blacklist) рядом
с программами по умолчанию стоит заглушка `example.exe`. Замените её своими программами:
откройте набор в «Настройках маршрутизации», затем правило, и впишите имена в поле
«Процесс (Linux/Windows)» — по одному в строке или через запятую.

Имена регистрозависимы: пишите точно как на вкладке «Подробности» диспетчера задач
(`Discord.exe`, а не `discord.exe`).

Пример — игры и Steam напрямую, в `Direct MY process` набора Whitelist:

```
VALORANT-Win64-Shipping.exe
RiotClientServices.exe
vgc.exe
dota2.exe
cs2.exe
steam.exe
steamservice.exe
steamwebhelper.exe
```

Первые три — игра Valorant, клиент Riot и античит Vanguard, последние три — клиент Steam,
его служба и встроенный браузер. В Blacklist игры и так идут напрямую.

### Торренты

Правило `bittorrent` узнаёт только открытое рукопожатие. Шифрованные соединения, DHT
и UDP-трекеры оно пропускает, и в Whitelist они уйдут в прокси. Поэтому qBittorrent
отправлен напрямую по имени процесса. Если у вас другой клиент, допишите его в
`Direct MY process`.

### Свой сервер мимо туннеля

В Whitelist есть выключенное правило `Direct MY IP`. Впишите IP сервера и включите его,
если ходите на сервер по SSH или вложенным клиентом.

## Почему отдельные правила под Xray TUN

В режиме Xray TUN v2rayN сам включает `routeOnly`. Xray видит у каждого соединения реальный
IP назначения и отдельно имя сайта из SNI.

- **`IPIfNonMatch`, а не `IPOnDemand`.** При `IPIfNonMatch` правила по IP проверяют реальный
  адрес, без DNS-запросов. При `IPOnDemand` Xray для каждого соединения с именем сначала
  резолвит это имя и сверяет правила по IP с ответом DNS, а не с реальным адресом.
  Отсюда задержка на новых соединениях (если DNS не отвечает — 4 секунды) и промахи правил
  по IP там, где имя в SNI не совпадает с адресом, например у Reality с чужим SNI.
- **Последнее правило — по IP** (`0.0.0.0/0`, `::/0`), а не по портам. В TUN оно срабатывает
  сразу, без DNS. Соединения, где известно только имя (системный прокси, SOCKS в программах),
  проходят второй круг: Xray резолвит имя и проверяет geoip-правила.

## Благодарности

- [runetfreedom/russia-v2ray-rules-dat](https://github.com/runetfreedom/russia-v2ray-rules-dat) —
  geo-файлы (`ru-blocked`, `category-ru`) и пресет «Россия» в v2rayN
- [v2fly/domain-list-community](https://github.com/v2fly/domain-list-community)
- [2dust/v2rayN](https://github.com/2dust/v2rayN), [XTLS/Xray-core](https://github.com/XTLS/Xray-core)

## Лицензия

[MIT](LICENSE)
