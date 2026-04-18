# Интеграция El'Cookie с Bitrix

Для Bitrix принято использовать стандартный префикс `BITRIX_SM_` в имени cookie.  
Рекомендуется сразу указать его в конфигурации El'Cookie:

```js
const CONFIG = {
    // ...
    COOKIE_NAME: 'BITRIX_SM_cookie_consent',
    // ...
};
```

## Пример checker-класса (PHP)

Ниже пример класса для серверной проверки согласий в Bitrix:

```php
<?php

declare(strict_types=1);

namespace App\Context;

use Bitrix\Main\Application;

final class CookieConsent
{
    private const COOKIE_NAME = 'cookie_consent';
    private static array $permissions = [];
    private static bool $loaded = false;

    private static function loadPermissions(): void
    {
        if (self::$loaded) {
            return;
        }

        $request = Application::getInstance()->getContext()->getRequest();
        $consent = (string) $request->getCookie(self::COOKIE_NAME);

        self::$permissions = array_values(array_filter(
            array_map('trim', explode(',', $consent)),
            static fn(string $value): bool => $value !== ''
        ));

        self::$loaded = true;
    }

    public static function hasConsent(string $category): bool
    {
        self::loadPermissions();

        return in_array($category, self::$permissions, true);
    }
}

```

## Примеры использования

```php
use App\Context\CookieConsent;

$hasMarketingConsent = CookieConsent::hasConsent('marketing');
$hasOtherConsent     = CookieConsent::hasConsent('other');

if ($hasMarketingConsent) {
    $asset->addString('
    <!-- Marketing counter -->
    <script src="/local/js/marketing-counter.js" defer></script>
    ');
}
```

> Аналогично можно проверять и другие категории перед подключением любых внешних скриптов.
