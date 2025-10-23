# scxcaptchaAjaxForm

Неблокирующая числовая CAPTCHA (5 цифр) для **AjaxForm** и **FormIt**.

Простая для человека, сложнее для ботов.

Все имена и классы имеют префикс `scx_`.

## Требования
- MODX Revolution **2.6+** или **3.x**
- PHP **7.2+** (рекомендуется **7.4**)
- **GD + TrueType** (для TTF-шрифта)

## Установка
1. Установите пакет через установщик MODX.
2. Проверьте наличие файла шрифта:
assets/components/scxcaptchaajaxform/fonts/DejaVuSansMono.ttf

Если его нет — будет использован bitmap-фолбэк.

## Быстрый старт

#### Вставьте блок капчи в форму (**некэшируемо!**):
```html
[[!ScxCaptchaAjaxForm]]
```

### Пример с AjaxForm
```html
[[!AjaxForm?
  &snippet=`FormIt`
  &form=`tpl.AjaxForm.example`
  &hooks=`ScxCaptchaAjaxFormHook,email`
  &validate=`name:required,email:required:email,scx_code:required`
]]
```

### Пример с FormIt
```html
[[!FormIt?
  &hooks=`ScxCaptchaAjaxFormHook,email`
  &validate=`name:required,email:required:email,scx_code:required`
]]
[[!ScxCaptchaAjaxForm]]
```

### Параметры сниппета
Параметр	    || Описание	                 ||  По умолчанию
ttl        	  || Время жизни токена (сек.) ||	1200
includeAssets	|| Как подключать CSS/JS:    || head, inline, none	head
render	      || Возвращать ли HTML блока: || 1 — да, 0 — только ассеты	

## Примеры параметров

### Обычная страница (ассеты в head):
```html
[[!ScxCaptchaAjaxForm]]
```

### Модалка (ассеты inline):
```html
[[!ScxCaptchaAjaxForm? &includeAssets=`inline`]]
```

### Подключить ассеты без разметки:
```html
[[!ScxCaptchaAjaxForm? &render=`0`]]
```

### Если CSS/JS уже подключены глобально:
```html
[[!ScxCaptchaAjaxForm? &includeAssets=`none`]]
```

## Поведение при первой загрузке
- Сессия не закрывается (совместимо с AjaxForm).
- При первом показе рендерится прозрачный GIF 1×1.
- Реальная капча загружается: по клику «Обновить», по клику на картинку, при фокусе поля ввода.

## Поля формы
Поле      || Назначение
scx_hp    || honeypot (должно быть пустым)
scx_ts	  || timestamp рендера формы (мс), защита ≥ 3 сек
scx_code	|| ответ пользователя (ровно 5 цифр)
scx_token	|| скрытый токен текущей капчи

## Как это работает
- Сниппет создаёт токен и сохраняет его в $_SESSION['scx_allowed'].
- JS подставляет src картинки по запросу пользователя.
- captcha.php генерирует код (5 цифр), хранит хэш в $_SESSION['scx_code'][TOKEN] и отдаёт PNG.
- Хук ScxCaptchaAjaxFormHook валидирует (honeypot, время, токен, код) и очищает данные.


## Подключение хука

### Добавьте в параметры:
```html
&hooks=`ScxCaptchaAjaxFormHook,...`
&validate=`name:required,email:required:email,scx_code:required`
```

## Отладка
Добавьте &debug=1 к URL картинки (captcha.php?...&debug=1).

### Если видите Bad token, проверьте:
- вызов капчи некэшируемо ([[!ScxCaptchaAjaxForm]]);
- страница и captcha.php на одном хосте/поддомене;
- ассеты подключены корректно (inline или глобально);
- форма AjaxForm проинициализирована (<input name="af_action">).
- 

### Глобальное подключение ассетов
```html
<link rel="stylesheet" href="[[++assets_url]]components/scxcaptchaajaxform/scx.css">
<script src="[[++assets_url]]components/scxcaptchaajaxform/scx.js"></script>
```

## Кастомизация

### Переопределяйте CSS-классы:

css
```
.scx-captcha, .scx-img, .scx-refresh, .scx-input, .scx-hp
```

## Безопасность
- Код хранится как хэш (password_hash).
- Токены короткоживущие и очищаются.
- Ответ — строго 5 цифр.


## Автор и поддержка
Разработчик: @saitmodx (телеграм)
Email: info@sait-modx.by

Если нашли баг/идею улучшения — напишите нам.

## Лицензия
MIT — см. репозиторий документации компонента:
https://github.com/wintik1/scxcaptchaAjaxForm-docs
