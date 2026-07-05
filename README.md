# GR-100 Tuner

Гитарный тюнер в стиле винтажной японской hi-fi техники 70-х: стрелочный VU-метр,
LED-индикатор, две темы (Cream / Midnight). Работает в браузере, ставится как PWA
и упаковывается в Android-приложение (TWA) для RuStore / Google Play.

**Live:** https://tuner-jet-sigma.vercel.app

## Как устроено

- **Чистая статика**: HTML + React 18 (UMD, лежит локально в `vendor/`) + CSS. Без сборщика.
- **Определение высоты ноты**: McLeod Pitch Method (NSDF) с гармонической коррекцией
  под гитарные струны — всё в `mobile.jsx`.
- **Тюнер струнный, не хроматический** (by design): стандартный строй E A D G B E,
  режимы AUTO / MANUAL.
- **PWA**: `manifest.json` + `sw.js` (network-first с офлайн-фолбэком) + иконки в
  `assets/icons/`.
- **TWA**: `.well-known/assetlinks.json` привязывает Android-пакет `ru.genrubit.tuner`
  к этому домену (полноэкранный режим без адресной строки).

## ⚠️ Главное правило разработки

`mobile.js` — это **скомпилированный** `mobile.jsx`. Браузер грузит только `mobile.js`.

После **любой** правки `mobile.jsx` обязательно:

```bash
npm install        # один раз, ставит Babel
npm run compile    # mobile.jsx → mobile.js
```

и коммить **оба** файла. Если править только `mobile.jsx` без компиляции —
на сайт уедет старый код.

## Деплой

Пуш в `main` → Vercel деплоит автоматически (~30–60 сек). Больше ничего делать не нужно.

## Структура

| Путь | Что это |
|---|---|
| `index.html` | оболочка: подключение React, mobile.js, регистрация SW |
| `mobile.jsx` | ВЕСЬ код приложения (UI + pitch detection) — править здесь |
| `mobile.js` | скомпилированный jsx — руками не трогать |
| `mobile.css` | все стили, включая ландшафтный режим и safe-area |
| `manifest.json`, `sw.js` | PWA |
| `assets/icons/` | иконки: квадратная (надпись GUITAR TUNER) + maskable (зелёный бар) |
| `assets/brand/` | логотип Gen Rubit Design Studio (профиль разработчика в сторах) |
| `.well-known/assetlinks.json` | привязка Android-пакета (отпечаток ключа подписи) |
| `privacy.html` | политика конфиденциальности (RU+EN) для сторов |
| `store-listing.md` | готовые тексты карточек RuStore / Google Play |

## Публикация в сторах

- Android-пакет собирается на [pwabuilder.com](https://pwabuilder.com) из live-URL:
  Package ID `ru.genrubit.tuner`, signing key — **всегда «Use mine»** с существующим
  keystore. Новый ключ создавать **нельзя**: сломается `assetlinks.json`.
- `signing.keystore` и пароли **в репозитории не хранятся** (секрет). Копии — у владельца
  (Google Диск, флешка).
- Обновления контента (звук, UI, фичи) доставляются простым пушем — пересборка APK
  нужна только при смене иконки, имени, разрешений или домена (не забудь поднять
  Version code).

## Дизайн

Дизайн финальный, пиксель-в-пиксель (Gen Rubit Design Studio) — не рестайлить.
Палитра циферблата: cream `#f4ead0→#d4bf86` / midnight `#1a3550`, чернила `#1a1612`,
красная зона `#c83a16` / `#ff8a4a`, зелёный «в строе» `#3ad17a`.

## Roadmap

- Хроматический режим (третий режим рядом с AUTO/MANUAL) — заодно откроет
  альтернативные строи (Drop D, Open G, DADGAD)
- Вибрация при попадании в строй
- Скриншоты в manifest.json (красивый диалог установки PWA)
