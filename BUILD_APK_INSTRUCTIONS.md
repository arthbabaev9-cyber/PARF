# 📱 Как собрать APK из этого проекта

## Что нужно установить (один раз)

### 1. Node.js (уже есть у вас)
Скачать: https://nodejs.org (версия 18+)

### 2. Android Studio
Скачать: https://developer.android.com/studio

При установке убедитесь что выбраны:
- ✅ Android SDK
- ✅ Android SDK Build-Tools
- ✅ Android SDK Platform (API 33 или новее)

### 3. Java JDK 17
Скачать: https://adoptium.net (Temurin JDK 17)

После установки добавить в PATH:
- Windows: `C:\Program Files\Eclipse Adoptium\jdk-17\bin`
- Mac: `export JAVA_HOME=$(/usr/libexec/java_home -v 17)`

---

## 🚀 Сборка APK (5 шагов)

### Шаг 1: Откройте папку проекта в терминале
```bash
cd путь/к/проекту
```

### Шаг 2: Установите зависимости
```bash
npm install
```

### Шаг 3: Соберите веб-приложение
```bash
npm run build
```

### Шаг 4: Добавьте Android платформу
```bash
npx cap add android
```

### Шаг 5: Синхронизируйте файлы и откройте в Android Studio
```bash
npx cap sync android
npx cap open android
```

---

## 📦 Создание APK файла

После открытия Android Studio:

### Для тестового APK (debug):
1. В меню: **Build → Build Bundle(s) / APK(s) → Build APK(s)**
2. Подождите 1-2 минуты
3. APK будет в: `android/app/build/outputs/apk/debug/app-debug.apk`

### Для финального APK (release):
1. **Build → Generate Signed Bundle / APK**
2. Выберите **APK**
3. Создайте ключ подписи (keystore):
   - Key store path: выберите папку для сохранения
   - Password: придумайте пароль
   - Alias: aromas-key
   - Заполните Name, Organization
4. Выберите **release**
5. Готовый APK: `android/app/build/outputs/apk/release/app-release.apk`

---

## 📲 Установка APK на телефон

### Способ 1: Через USB
1. Включите на телефоне: **Настройки → О телефоне → 7 раз нажмите "Номер сборки"** (включится режим разработчика)
2. **Настройки → Разработчик → USB-отладка** (включить)
3. Подключите телефон кабелем
4. В Android Studio нажмите зелёную кнопку ▶️ Run

### Способ 2: Передача файла
1. Скопируйте `app-debug.apk` на телефон
2. На телефоне: **Настройки → Безопасность → Установка из неизвестных источников** (разрешить)
3. Откройте файл APK через файловый менеджер
4. Нажмите «Установить»

---

## 🔄 Быстрая команда (всё сразу)

```bash
npm install && npm run build && npx cap add android && npx cap sync android && npx cap open android
```

После этого в Android Studio: Build → Build APK

---

## ❓ Частые проблемы

**Ошибка: "JAVA_HOME not found"**
→ Установите JDK 17 и добавьте в переменные среды

**Ошибка: "SDK location not found"**
→ Откройте Android Studio → Settings → Languages & Frameworks → Android SDK → укажите путь

**Ошибка: "Gradle sync failed"**
→ File → Invalidate Caches / Restart

**APK не устанавливается**
→ Разрешите установку из неизвестных источников в настройках телефона

---

## 🌐 Альтернатива: PWA Builder (без Android Studio)

Если не хотите устанавливать Android Studio:

1. Разместите сайт на хостинге (Vercel, Netlify — бесплатно)
2. Перейдите на https://www.pwabuilder.com
3. Вставьте URL вашего сайта
4. Нажмите **Package for stores → Android**
5. Скачайте готовый APK

Это самый простой способ!
