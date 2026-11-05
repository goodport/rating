Laravel Eloquent Rating
=======================

Laravel Eloquent Rating allows you to assign ratings to any model, just like any other review based on stars.

> This is the [Goodport](https://github.com/goodport) fork of
> [rennokki/rating](https://github.com/rennokki/rating), updated for Laravel 12.
> The original package and documentation were written by Alex Renoki.

## Requirements

- PHP 8.2+
- Laravel 12

## Installation

Install the package:

```bash
composer require goodport/rating
```

Publish the config:

```bash
php artisan vendor:publish --provider="Rennokki\Rating\RatingServiceProvider" --tag="config"
```

Publish the migrations:

```bash
php artisan vendor:publish --provider="Rennokki\Rating\RatingServiceProvider" --tag="migrations"
```

## Preparing the model

To allow a model to rate other models, it should use the `CanRate` trait and implement the `Rater` contract.

```php
use Rennokki\Rating\Traits\CanRate;
use Rennokki\Rating\Contracts\Rater;

class User extends Model implements Rater
{
    use CanRate;
    ...
}
```

The other models that can be rated should use `CanBeRated` trait and `Rateable` contract.

```php
use Rennokki\Rating\Traits\CanBeRated;
use Rennokki\Rating\Contracts\Rateable;

class User extends Model implements Rateable
{
    use CanBeRated;
    ...
}
```

If your model can both rate & be rated by other models, you should use `Rate` trait and `Rating` contract.

```php
use Rennokki\Rating\Traits\Rate;
use Rennokki\Rating\Contracts\Rating;

class User extends Model implements Rating
{
    use Rate;

    //
}
```

## Usage

To rate other models, simply call `rate()` method:

```php
$page = Page::find(1);

$user->rate($page, 10);
$user->hasRated($page); // true
$page->averageRating(User::class); // 10.0, as float
```

As a second argument to the `rate()` method, you can pass the rating score. It can either be string, integer or float.

To update a rating, you can call `updateRatingFor()` method:

```php
$user->updateRatingFor($page, 9);
$page->averageRating(User::class); // 9.00, as float
```

As you have seen, you can call `averageRating()` within models that can be rated. The return value is the average arithmetic value of all ratings as `float`.

If we leave the argument empty, we will get `0.00` because no other `Page` model has rated the page so far. But since users have rated the page, we will calculate the average only from the `User` models, since only they have voted the page, strictly by passing the class name as the argument.

```php
$page = Page::find(1);

$user1->rate($page, 10);
$user2->rate($page, 6);

$page->averageRating(); // 0.00
$page->averageRating(User::class); // 8.00, as float
```

While in our example, the `User` class can both rate and be rated, we can leave the argument empty if we reference to its class:

```php
$user = User::find(1);

$user1->rate($user, 10);
$user2->rate($user, 6);

$user->averageRating(); // 8.00, as float
$user->averageRating(User::class); // 8.00, it is equivalent
```

The relationships are based on this too:

```php
$page->raters()->get(); // Pages that have rated this page
$page->raters(User::class)->get(); // Users that have rated this page

$user->ratings()->get(); // Users that this user has rated
$user->ratings(Page::class)->get(); // Pages that this user has rated
```

## Testing

```bash
composer test
```

## Contributing

Please see [CONTRIBUTING](CONTRIBUTING.md) for details.

## Security

If you discover a security issue, please report it privately through
[GitHub Security Advisories](https://github.com/goodport/rating/security/advisories/new)
instead of using the public issue tracker.

## Credits

- [Alex Renoki](https://github.com/rennokki) — original author of `rennokki/rating`
- [Goodport](https://github.com/goodport) — Laravel 12 fork
- [All Contributors](../../contributors)
