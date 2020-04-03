---
title: Facepile
extends: _layouts.documentation
section: main
lang: ru
menu: layouts
---

`Facepile` показывает список аватаров в горизонтальном виде. Каждый круг представляет человека. Используйте этот `layout` при отображении совместного доступа к определённому представлению, файлу или задачи.

Карточки используются для упаковки небольшого количества контента. Обычно содержат заголовок, короткий абзац и кнопки управления. 

![facepile](https://orchid.software/assets/img/layouts/facepile.png)

`Facepile` принимает набор обьектов которые реализуют интерфейс `Personable`.

> В этом примере используется [представители](/ru/docs/presenters), настоятельно рекомендуеться ознакомится с ними.
 
Вместо создания отдельного класса рекомендуется использоватье представителей для `Eloquent` моделей, например:

```php
namespace App;

use App\Presenters\EmployeePresenter;
use Illuminate\Database\Eloquent\Client;

class Employee extends Model
{
    protected $fillable = [
        'name',
        'avatar',
        'position',
    ];

    public function presenter(): EmployeePresenter
    {
        return new EmployeePresenter($this);
    }
}
```

Можно записать представителя:

```php
namespace App\Presenters;

use Orchid\Screen\Contracts\Personable;
use Orchid\Support\Presenter;

class EmployeePresenter extends Presenter implements Cardable
{

    public function title(): string
    {
        return $this->entity->name;
    }

    public function subTitle(): string
    {
        return $this->entity->position;
    }

    public function url(): string
    {
        return route('platform.systems.users.edit', $this->entity);
    }

    public function image(): ?string
    {
        return $this->entity->avatar;
    }
}
```

Использование в экране будет следующим:

```php
use Orchid\Screen\Layouts\Facepile;

/**
 * Query data.
 *
 * @return array
 */
public function query(): array
{
    return [
        'avatars' => Employee::limit(8)->get()->map->presenter(),
    ];
}

/**
 * Views.
 *
 * @return array
 * @throws \Throwable
 *
 */
public function layout(): array
{
    return [
        new Facepile('avatars'),
    ];
}
```