# AGENTS.md

## About the package

`goodport/rating` is a public fork of `rennokki/rating` for Laravel 12: star ratings for any Eloquent model through the polymorphic `ratings` table.

## Stack

- PHP 8.2+
- Laravel 12 (`illuminate/database`, `illuminate/support`)
- Tests: Orchestra Testbench, PHPUnit 11, `laravel/legacy-factories`
- Formatting: Laravel Pint is not installed; style follows the `laravel` preset from `.styleci.yml`

## Structure

- `src/Contracts/` — public interfaces `Rater`, `Rateable`, `Rating`.
- `src/Traits/CanRate.php` — ratings a model gives.
- `src/Traits/CanBeRated.php` — ratings a model receives and its average rating.
- `src/Traits/Rate.php` — combines `CanRate` and `CanBeRated`.
- `src/Models/RaterModel.php` — model for a row in the `ratings` table.
- `src/RatingServiceProvider.php` — publishes the config and migration.
- `database/migrations/2018_07_14_183253_ratings.php` — migration for the `ratings` table.
- `config/rating.php` — publishable config.
- `tests/` — Orchestra Testbench tests with sqlite and legacy factories.

## Public API

- `rate($model, float $rating)` — rate a model.
- `updateRatingFor($model, $newRating)` — update an existing rating.
- `unrate($model)` — remove a rating.
- `hasRated($model)` — check whether the current object has rated the model.
- `ratings($model = null)` — relation to rated models.
- `raters($model = null)` — relation to models that rated this model.
- `averageRating($model = null)` — average rating as `float`.

## Commands

- `composer install`
- `composer test` or `vendor/bin/phpunit`

## Tests

- Use Orchestra Testbench, sqlite, and legacy factories.
- No real databases or network access required.
- Run only the tests related to your change.

## Rules

- Do not rename namespaces, contracts, traits, or the model without a separate major task: it breaks backward compatibility.
- Preserve the signatures and behavior of public methods.
- Use polymorphic `morphToMany` relations; do not replace them with manual foreign-key queries.
- Maintain PHP 8.2+ compatibility: do not use syntax available only since 8.3.
- This file describes the package structure; update it when the architecture changes.
