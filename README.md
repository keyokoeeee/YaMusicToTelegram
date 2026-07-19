🎧 YaMusicToTelegram

Автопостинг текущего трека из Яндекс.Музыки в Telegram-канал: обложка, прогресс-бар, статус паузы — без Python и сторонних пакетов, только PowerShell.

📌 О проекте

YaMusicToTelegram — PowerShell-скрипт для Windows, который следит за тем, что сейчас играет в Яндекс.Музыке, и автоматически публикует это в Telegram-канал: обложка, артист, название, альбом, живой прогресс-бар с таймером. При смене трека старый пост удаляется и публикуется новый.

Определение трека — через встроенный в Windows WinRT Media API (тот же механизм, что рисует медиа-оверлей при нажатии Fn-клавиш громкости). Никакого Python, winsdk или сторонних пакетов — только чистый PowerShell.

✨ Возможности


🎵 Автоопределение трека — через системный WinRT Media API Windows
🖼 Обложка и точная длительность — подтягиваются из каталога api.music.yandex.net
▶️ Живой прогресс-бар с таймером, который обновляется прямо в посте
⏸ Отдельное состояние паузы — публикуется своя картинка вместо обложки
🔇 Отдельное состояние простоя — когда плеер закрыт или ничего не играет
🧾 Лог событий (track_events.log) — старт трека, пауза, возобновление, сброс таймера
🛡 Защита от 429 Too Many Requests — ограничение частоты правок + корректная обработка retry_after
🖱 Отключение QuickEdit Mode консоли — случайный клик по окну больше не ставит скрипт «на паузу»
⏱ Таймауты на все HTTP-запросы — скрипт не виснет при проблемах сети


🖼 Как это выглядит

СостояниеЧто в посте▶️ ИграетОбложка трека, артист — название, альбом, прогресс-бар, таймер⏸ ПаузаСвоя картинка (pause_cover.png), артист — название, без прогресс-бара🔇 Ничего не играетТа же картинка, что и для паузы, с соответствующей подписью

🧰 Требования


Windows 10/11
PowerShell 5.1 (уже встроен в Windows) или PowerShell 7+
Яндекс.Музыка — десктопное приложение или веб/UWP, лишь бы сессия была видна системному медиа-центру Windows
Telegram-бот, добавленный администратором в канал с правом публикации сообщений


🚀 Установка


Скачайте YaMusicToTelegram.ps1 и pause_cover.png в одну папку.
Создайте бота через @BotFather → получите токен вида 123456789:AAA....
Добавьте бота администратором в канал (обязательно право «Публиковать сообщения»).
Узнайте chat_id канала — перешлите любое сообщение из канала боту @userinfobot, либо откройте https://api.telegram.org/bot<ТОКЕН>/getUpdates после того, как в канал придёт новое сообщение. Для каналов chat_id обычно отрицательный и начинается с -100.
Впишите токен и chat_id — см. Настройку ниже.
Запустите:


powershell   powershell -ExecutionPolicy Bypass -File YaMusicToTelegram.ps1

⚙️ Настройка

Все пользовательские настройки — в блоке НАСТРОЙКИ в начале файла, строки 21–33.

<table>
<tr><th>Строка</th><th>Параметр</th><th>Описание</th></tr>
<tr>
<td align="center"><b>22</b></td>
<td><code>$TelegramBotToken</code></td>
<td>🔑 Токен вашего бота от BotFather<br><code>$TelegramBotToken = "123456789:AAExxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"</code></td>
</tr>
<tr>
<td align="center"><b>23</b></td>
<td><code>$TelegramChatId</code></td>
<td>💬 Id канала для публикации<br><code>$TelegramChatId = "-1001234567890"</code></td>
</tr>
<tr>
<td align="center">26</td>
<td><code>$AppNameFilter</code></td>
<td>По этой подстроке скрипт находит нужную медиа-сессию среди всех, что видит Windows. Менять не нужно при использовании десктопного приложения Яндекс.Музыки</td>
</tr>
<tr>
<td align="center">30</td>
<td><code>$PauseImagePath</code></td>
<td>Путь к картинке для паузы/простоя — по умолчанию <code>pause_cover.png</code> рядом со скриптом</td>
</tr>
</table>

Только строки 22 и 23 обязательны для запуска — без них скрипт не заработает. Остальные параметры (25, 27–29, 31–32) технические, с пояснением прямо в комментариях рядом; трогать не обязательно.



⚠️ Перед публикацией на GitHub


[!CAUTION]
Токен бота — это полноценный пароль от него. Любой, кто увидит его в публичном репозитории, сможет постить и удалять сообщения от имени вашего бота.




Замените строки 22–23 на плейсхолдеры перед коммитом:


powershell   $TelegramBotToken = "ВАШ_ТОКЕН_ОТ_BOTFATHER"
   $TelegramChatId   = "ВАШ_CHAT_ID"


Если токен уже где-то «засветился» — отзовите его через @BotFather → /revoke и выпустите новый. Просто заменить значение в скрипте недостаточно.
Добавьте в .gitignore файлы, которые скрипт создаёт сам во время работы:


gitignore   last_message_id.txt
   track_events.log

Более безопасный вариант — не хранить токен в самом скрипте, а брать его из переменной окружения:

powershell$TelegramBotToken = $env:YAMUSIC_BOT_TOKEN
$TelegramChatId   = $env:YAMUSIC_CHAT_ID

Тогда сам файл можно публиковать вообще без правок — значения задаются в системе перед запуском.

📁 Структура файлов

📦 YaMusicToTelegram
 ┣ 📜 YaMusicToTelegram.ps1   ← сам скрипт
 ┣ 🖼 pause_cover.png          ← картинка для паузы/простоя (обязательна)
 ┣ 📄 last_message_id.txt      ← создаётся автоматически: id текущего поста
 ┗ 📄 track_events.log         ← создаётся автоматически: лог событий

⚡ Как это работает

#mermaid-r1je-r2 { font-family: "Anthropic Sans", system-ui, "Segoe UI", Roboto, Helvetica, Arial, sans-serif; font-size: 16px; fill: rgb(229, 229, 229); }
#mermaid-r1je-r2 .edge-animation-slow { stroke-dashoffset: 900; animation: 50s linear 0s infinite normal none running dash; stroke-linecap: round; stroke-dasharray: 9, 5 !important; }
#mermaid-r1je-r2 .edge-animation-fast { stroke-dashoffset: 900; animation: 20s linear 0s infinite normal none running dash; stroke-linecap: round; stroke-dasharray: 9, 5 !important; }
#mermaid-r1je-r2 .error-icon { fill: rgb(204, 120, 92); }
#mermaid-r1je-r2 .error-text { fill: rgb(51, 135, 163); stroke: rgb(51, 135, 163); }
#mermaid-r1je-r2 .edge-thickness-normal { stroke-width: 1px; }
#mermaid-r1je-r2 .edge-thickness-thick { stroke-width: 3.5px; }
#mermaid-r1je-r2 .edge-pattern-solid { stroke-dasharray: 0; }
#mermaid-r1je-r2 .edge-thickness-invisible { stroke-width: 0; fill: none; }
#mermaid-r1je-r2 .edge-pattern-dashed { stroke-dasharray: 3; }
#mermaid-r1je-r2 .edge-pattern-dotted { stroke-dasharray: 2; }
#mermaid-r1je-r2 .marker { fill: rgb(161, 161, 161); stroke: rgb(161, 161, 161); }
#mermaid-r1je-r2 .marker.cross { stroke: rgb(161, 161, 161); }
#mermaid-r1je-r2 svg { font-family: "Anthropic Sans", system-ui, "Segoe UI", Roboto, Helvetica, Arial, sans-serif; font-size: 16px; }
#mermaid-r1je-r2 p { margin: 0px; }
#mermaid-r1je-r2 .label { font-family: "Anthropic Sans", system-ui, "Segoe UI", Roboto, Helvetica, Arial, sans-serif; color: rgb(229, 229, 229); }
#mermaid-r1je-r2 .cluster-label text { fill: rgb(51, 135, 163); }
#mermaid-r1je-r2 .cluster-label span { color: rgb(51, 135, 163); }
#mermaid-r1je-r2 .cluster-label span p { background-color: transparent; }
#mermaid-r1je-r2 .label text, #mermaid-r1je-r2 span { fill: rgb(229, 229, 229); color: rgb(229, 229, 229); }
#mermaid-r1je-r2 .node rect, #mermaid-r1je-r2 .node circle, #mermaid-r1je-r2 .node ellipse, #mermaid-r1je-r2 .node polygon, #mermaid-r1je-r2 .node path { fill: transparent; stroke: rgb(161, 161, 161); stroke-width: 1px; }
#mermaid-r1je-r2 .rough-node .label text, #mermaid-r1je-r2 .node .label text, #mermaid-r1je-r2 .image-shape .label, #mermaid-r1je-r2 .icon-shape .label { text-anchor: middle; }
#mermaid-r1je-r2 .node .katex path { fill: rgb(0, 0, 0); stroke: rgb(0, 0, 0); stroke-width: 1px; }
#mermaid-r1je-r2 .rough-node .label, #mermaid-r1je-r2 .node .label, #mermaid-r1je-r2 .image-shape .label, #mermaid-r1je-r2 .icon-shape .label { text-align: center; }
#mermaid-r1je-r2 .node.clickable { cursor: pointer; }
#mermaid-r1je-r2 .root .anchor path { stroke-width: 0; stroke: rgb(161, 161, 161); fill: rgb(161, 161, 161) !important; }
#mermaid-r1je-r2 .arrowheadPath { fill: rgb(11, 11, 11); }
#mermaid-r1je-r2 .edgePath .path { stroke: rgb(161, 161, 161); stroke-width: 1px; }
#mermaid-r1je-r2 .flowchart-link { stroke: rgb(161, 161, 161); fill: none; }
#mermaid-r1je-r2 .edgeLabel { background-color: transparent; text-align: center; }
#mermaid-r1je-r2 .edgeLabel p { background-color: transparent; }
#mermaid-r1je-r2 .edgeLabel rect { opacity: 0.5; background-color: transparent; fill: transparent; }
#mermaid-r1je-r2 .labelBkg { background-color: rgba(0, 0, 0, 0.5); }
#mermaid-r1je-r2 .cluster rect { fill: rgb(204, 120, 92); stroke: rgb(138, 115, 107); stroke-width: 1px; }
#mermaid-r1je-r2 .cluster text { fill: rgb(51, 135, 163); }
#mermaid-r1je-r2 .cluster span { color: rgb(51, 135, 163); }
#mermaid-r1je-r2 div.mermaidTooltip { position: absolute; text-align: center; max-width: 200px; padding: 2px; font-family: "Anthropic Sans", system-ui, "Segoe UI", Roboto, Helvetica, Arial, sans-serif; font-size: 12px; background: rgb(204, 120, 92); border: 1px solid rgb(138, 115, 107); border-radius: 2px; pointer-events: none; z-index: 100; }
#mermaid-r1je-r2 .flowchartTitleText { text-anchor: middle; font-size: 18px; fill: rgb(229, 229, 229); }
#mermaid-r1je-r2 rect.text { fill: none; stroke-width: 0; }
#mermaid-r1je-r2 .icon-shape, #mermaid-r1je-r2 .image-shape { background-color: transparent; text-align: center; }
#mermaid-r1je-r2 .icon-shape p, #mermaid-r1je-r2 .image-shape p { background-color: transparent; padding: 2px; }
#mermaid-r1je-r2 .icon-shape .label rect, #mermaid-r1je-r2 .image-shape .label rect { opacity: 0.5; background-color: transparent; fill: transparent; }
#mermaid-r1je-r2 .label-icon { display: inline-block; height: 1em; overflow: visible; vertical-align: -0.125em; }
#mermaid-r1je-r2 .node .label-icon path { fill: currentcolor; stroke: revert; stroke-width: revert; }
#mermaid-r1je-r2 .node .neo-node { stroke: rgb(161, 161, 161); }
#mermaid-r1je-r2 [data-look="neo"].node rect, #mermaid-r1je-r2 [data-look="neo"].cluster rect, #mermaid-r1je-r2 [data-look="neo"].node polygon { stroke: url("#mermaid-r1je-r2-gradient"); filter: drop-shadow(rgb(185, 185, 185) 1px 2px 2px); }
#mermaid-r1je-r2 [data-look="neo"].node path { stroke: url("#mermaid-r1je-r2-gradient"); stroke-width: 1px; }
#mermaid-r1je-r2 [data-look="neo"].node .outer-path { filter: drop-shadow(rgb(185, 185, 185) 1px 2px 2px); }
#mermaid-r1je-r2 [data-look="neo"].node .neo-line path { stroke: rgb(161, 161, 161); filter: none; }
#mermaid-r1je-r2 [data-look="neo"].node circle { stroke: url("#mermaid-r1je-r2-gradient"); filter: drop-shadow(rgb(185, 185, 185) 1px 2px 2px); }
#mermaid-r1je-r2 [data-look="neo"].node circle .state-start { fill: rgb(0, 0, 0); }
#mermaid-r1je-r2 [data-look="neo"].icon-shape .icon { fill: url("#mermaid-r1je-r2-gradient"); filter: drop-shadow(rgb(185, 185, 185) 1px 2px 2px); }
#mermaid-r1je-r2 [data-look="neo"].icon-shape .icon-neo path { stroke: url("#mermaid-r1je-r2-gradient"); filter: drop-shadow(rgb(185, 185, 185) 1px 2px 2px); }
#mermaid-r1je-r2 :root { --mermaid-font-family: "Anthropic Sans",system-ui,"Segoe UI",Roboto,Helvetica,Arial,sans-serif; }PlayingPausedНет сессииОпрос сессии Windows разв 1 секСтатус?Обложка изЯндекс.Музыки+ прогресс-барКартинка паузыpause_cover.pngКартинка простояpause_cover.pngПубликация / правкапоста в TelegramЗапись события вtrack_events.log


Раз в секунду скрипт спрашивает Windows, какой трек активен в сессии, подходящей под $AppNameFilter.
Определяется состояние: Playing / Paused / Idle.
При смене трека или входе/выходе из паузы — старый пост удаляется, публикуется новый, событие пишется в лог, таймер сбрасывается (на паузе — «замораживается»).
Пока трек тот же — пост не пересоздаётся, а раз в $CaptionUpdateIntervalSeconds секунд правится подпись с прогресс-баром.
Картинка паузы/простоя грузится в Telegram один раз, дальше переиспользуется её file_id — без повторной загрузки файла.


🔧 Устранение проблем

<details>
<summary><b>429 Too Many Requests</b></summary><br>
Telegram ограничивает частоту правок одного сообщения. Увеличьте $CaptionUpdateIntervalSeconds (строка 32), например до 8–10.

</details>
<details>
<summary><b>Скрипт «зависает»</b></summary><br>
Чаще всего это не зависание, а Windows-консоль ставит процесс на паузу при клике мышкой по окну (QuickEdit Mode). Скрипт отключает этот режим сам при запуске; если всё равно происходит — не кликайте по окну консоли во время работы.

</details>
<details>
<summary><b>Нет обложки трека</b></summary><br>
Яндекс.Музыка не нашла трек по запросу «артист + название» в своём каталоге (редкий ремикс, неточно указан артист). Пост в этом случае публикуется без картинки.

</details>
<details>
<summary><b>«Ни одна сессия не подошла под фильтр»</b></summary><br>
Windows видит медиа-сессию, но её SourceAppUserModelId не содержит yandex. Включите $VerboseLog = $true (строка 28) — в консоли появится список всех найденных сессий, и $AppNameFilter можно поправить под конкретный случай.

</details>
📄 Лицензия

MIT — используйте, меняйте, распространяйте свободно.


Сделано для тех, кто хочет делиться музыкой из Яндекс.Музыки в Telegram ✨
