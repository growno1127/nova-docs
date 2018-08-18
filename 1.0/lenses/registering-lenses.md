# Registering Lenses

[[toc]]

Once you have defined a lens, you are ready to attach it to a resource. Each resource generated by Nova contains a `lenses` method. To attach a lens to a resource, you should simply add it to the array of lenses returned by this method:

```php
/**
 * Get the lenses available for the resource.
 *
 * @param  \Illuminate\Http\Request  $request
 * @return array
 */
public function lenses(Request $request)
{
    return [new Lenses\MostValuableUsers];
}
```

## Authorization

If you would like to only expose a given lens to certain users, you may chain the `canSee` method onto your lens registration. The `canSee` method accepts a Closure which should return `true` or `false`. The Closure will receive the incoming HTTP request:

```php
use App\User;

/**
 * Get the lenses available for the resource.
 *
 * @param  \Illuminate\Http\Request  $request
 * @return array
 */
public function lenses(Request $request)
{
    return [
        (new Lenses\MostValuableUsers)->canSee(function ($request) {
            return $request->user()->can(
                'viewValuableUsers', User::class
            );
        }),
    ];
}
```

In the example above, we are using Laravel's `Authorizable` trait's `can` method on our `User` model to determine if the authorized user is authorized for the `viewValuableUsers` action. However, since proxying to authorization policy methods is a common use-case for `canSee`, you may use the `canSeeWhen` method to achieve the same behavior. The `canSeeWhen` method has the same method signature as the `Illuminate\Foundation\Auth\Access\Authorizable` trait's `can` method:

```php
/**
 * Get the lenses available for the resource.
 *
 * @param  \Illuminate\Http\Request  $request
 * @return array
 */
public function lenses(Request $request)
{
    return [
        (new Lenses\MostValuableUsers)->canSeeWhen(
            'viewValuableUsers', User::class
        ),
    ];
}
```