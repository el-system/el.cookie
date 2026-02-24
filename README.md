# El'Cookie

Легковесный JavaScript-плагин для управления cookie-файлами с поддержкой категорий и пользовательских настроек.

## О проекте

**El'Cookie** — компактное и независимое решение для реализации cookie-баннера и управления согласием пользователей в соответствии с требованиями GDPR, CCPA и других регуляторных норм.

Плагин позволяет пользователю выбирать, какие категории cookie разрешены, а разработчику — безопасно инициализировать скрипты только при наличии соответствующего согласия.

## Ключевые возможности

- Поддержка категорий cookie (marketing, other и др.)
- Проверка согласия перед инициализацией скриптов
- Ленивая инициализация DOM-элементов и стилей
- Отсутствие внешних зависимостей
- Использование `localStorage` и/или cookie для хранения согласия
- Обход блокировщиков через динамические селекторы
- Адаптивный минималистичный интерфейс

## Принцип работы

El'Cookie использует механизм ленивой инициализации:

- DOM-элементы и стили создаются только в момент отображения баннера или окна настроек
- После закрытия все созданные элементы удаляются из DOM
- Стили автоматически очищаются при отсутствии активных компонентов

Это позволяет минимизировать влияние плагина на производительность страницы при первоначальной загрузке.

## Установка

Подключите скрипт перед закрывающим тегом `</body>`:

```html
<script src="el.cookie.js"></script>
```

Поменяйте основной цвет --cc-color-main под стиль проекта.

## Использование

### Проверка согласия (JavaScript)

Для выполнения кода только при наличии разрешения используйте метод PermissionChecker.check:

```js
PermissionChecker.check('marketing', () => {
    console.log('Маркетинговые cookie разрешены');
});

PermissionChecker.check('other', () => {
    console.log('Дополнительные cookie разрешены');
});
```

Код внутри callback-функции будет выполнен только при наличии согласия на соответствующую категорию.

### Проверка согласия (PHP)

Если необходимо управлять загрузкой скриптов на стороне сервера, можно использовать следующий пример:

```php
class CookieConsent
{
    private static array $permissions = [];

    private static function loadPermissions(): void
    {
        if (empty(self::$permissions)) {
            $consent = $_COOKIE['cookie_consent'] ?? '';
            self::$permissions = array_filter(explode(',', $consent));
        }
    }

    public static function hasConsent(string $category): bool
    {
        self::loadPermissions();
        return in_array($category, self::$permissions, true);
    }
}

// Usage
if (CookieConsent::hasConsent('marketing')) {
    echo '<script src="marketing.js"></script>';
}

if (CookieConsent::hasConsent('other')) {
    echo '<script src="additional-features.js"></script>';
}
```

## Открытие окна настроек

Открыть окно настроек можно программно:

`window.openCookieSettings();`

Или через добавление класса:

```html
<div class="open-cookie-settings">Настройки cookie</div>
```

## Лицензия

MIT License — свободное использование, модификация и распространение.

**Автор**

Andrey Shuin
El’System © 2025