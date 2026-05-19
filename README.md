# Laravel Relationship Events

Missing relationship events for Laravel — actively maintained fork of [chelout/laravel-relationship-events](https://github.com/chelout/laravel-relationship-events)

> **Note:** The original package by [@chelout](https://github.com/chelout) has been inactive since 2020. This fork picks up where it left off, with Laravel 13 support and active maintenance.

<p align="center">
 <a href="https://github.com/sameedkun/laravel-relationship-events/actions"><img src="https://github.com/sameedkun/laravel-relationship-events/workflows/tests/badge.svg" alt="Build Status"></a>
 <a href="https://packagist.org/packages/sameedkun/laravel-relationship-events"><img src="https://poser.pugx.org/sameedkun/laravel-relationship-events/d/total.svg" alt="Total Downloads"></a>
 <a href="https://packagist.org/packages/sameedkun/laravel-relationship-events"><img src="https://poser.pugx.org/sameedkun/laravel-relationship-events/v/stable.svg" alt="Latest Stable Version"></a>
 <a href="https://packagist.org/packages/sameedkun/laravel-relationship-events"><img src="https://poser.pugx.org/sameedkun/laravel-relationship-events/license.svg" alt="License"></a>
</p>

## Compatibility

| Package | Laravel | PHP |
|---------|---------|-----|
| `v5.x`  | `12.x`, `13.x` | `^8.3` |
| `v4.x`  | `11.x`, `12.x` | `^8.2` |

## Migrating from `chelout/laravel-relationship-events`

Already using the original package? Switching is seamless — no code changes required. The namespace, traits, and events are all identical.

Just swap the package name in your `composer.json`:

```bash
composer remove chelout/laravel-relationship-events
composer require sameedkun/laravel-relationship-events
```

## Install

1. Install package with composer

```bash
composer require sameedkun/laravel-relationship-events
```

2. Use necessary trait in your model.

#### Available traits:
- HasOneEvents
- HasBelongsToEvents
- HasManyEvents
- HasBelongsToManyEvents
- HasMorphOneEvents
- HasMorphToEvents
- HasMorphManyEvents
- HasMorphToManyEvents
- HasMorphedByManyEvents

```php
use Chelout\RelationshipEvents\Concerns\HasOneEvents;
use Illuminate\Database\Eloquent\Model;

class User extends Model
{
    use HasOneEvents;

    public static function boot()
    {
        parent::boot();

        static::hasOneSaved(function ($parent, $related) {
            dump('hasOneSaved', $parent, $related);
        });

        static::hasOneUpdated(function ($parent, $related) {
            dump('hasOneUpdated', $parent, $related);
        });
    }
}
```

```php
use Chelout\RelationshipEvents\Concerns\HasMorphToManyEvents;
use Illuminate\Database\Eloquent\Model;

class Post extends Model
{
    use HasMorphToManyEvents;

    public static function boot()
    {
        parent::boot();

        static::morphToManyAttached(function ($relation, $parent, $ids, $attributes) {
            dump('morphToManyAttached', $relation, $parent, $ids, $attributes);
        });

        static::morphToManyDetached(function ($relation, $parent, $ids) {
            dump('morphToManyDetached', $relation, $parent, $ids);
        });
    }

    public function tags()
    {
        return $this->morphToMany(Tag::class, 'taggable');
    }
}
```

3. Dispatchable relationship events.

It is possible to fire event classes via `$dispatchesEvents` property and adding `HasDispatchableEvents` trait:

```php
use Chelout\RelationshipEvents\Concerns\HasOneEvents;
use Chelout\RelationshipEvents\Traits\HasDispatchableEvents;
use Illuminate\Database\Eloquent\Model;

class User extends Model
{
    use HasDispatchableEvents;
    use HasOneEvents;

    protected $dispatchesEvents = [
        'hasOneSaved' => HasOneSaved::class,
    ];
}
```

## Relationships
- [One To One Relations](doc/1-one-to-one.md)
- [One To Many Relations](doc/2-one-to-many.md)
- [Many To Many Relations](doc/3-many-to-many.md)
- [Has Many Through Relations](doc/4-has-many-through.md)
- [One To One Polymorphic Relations](doc/5-one-to-one-polymorphic.md)
- [One To Many Polymorphic Relations](doc/6-one-to-many-polymorphic.md)
- [Many To Many Polymorphic Relations](doc/7-many-to-many-polymorphic.md)

## Observers

Starting from v0.4 it is possible to use relationship events in [Laravel observer classes](https://laravel.com/docs/eloquent#observers). Add `HasRelationshipObservables` trait to your model and define an observer:

```php
namespace App\Observer;

class UserObserver
{
    public function hasOneCreating(User $user, Model $related)
    {
        Log::info("Creating profile for user {$related->name}.");
    }

    public function hasOneCreated(User $user, Model $related)
    {
        Log::info("Profile for user {$related->name} has been created.");
    }
}
```

Register the observer in `AppServiceProvider`:

```php
public function boot()
{
    User::observe(UserObserver::class);
}
```

## Credits

- Original package by [@chelout](https://github.com/chelout) — [chelout/laravel-relationship-events](https://github.com/chelout/laravel-relationship-events)
- Maintained by [@sameedkun](https://github.com/sameedkun)

## License

MIT