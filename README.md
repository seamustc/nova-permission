# A Laravel Nova tool for Spatie's laravel-permission library

[![Latest Version on Packagist](https://img.shields.io/packagist/v/jeffersonsimaogoncalves/nova-permission.svg?style=flat-square)](https://packagist.org/packages/jeffersonsimaogoncalves/nova-permission)
[![Total Downloads](https://img.shields.io/packagist/dt/jeffersonsimaogoncalves/nova-permission.svg?style=flat-square)](https://packagist.org/packages/jeffersonsimaogoncalves/nova-permission)

![screenshot 1](https://raw.githubusercontent.com/jeffersonsimaogoncalves/nova-permission/master/docs/user-resource.png)

## Installation

You can install the package in to a Laravel app that uses [Nova](https://nova.laravel.com) via composer:

```bash
composer require jeffersonsimaogoncalves/nova-permission
```

Go through the [Installation](https://spatie.be/docs/laravel-permission/v5/introduction) section in order to setup [laravel-permission](https://packagist.org/packages/spatie/laravel-permission).

Next up, you must register the tool with Nova. This is typically done in the `tools` method of the `NovaServiceProvider`.

```php
// in app/Providers/NovaServiceProvider.php

// ...

public function tools()
{
    return [
        // ...
        \JeffersonSimaoGoncalves\NovaPermission\NovaPermissionTool::make(),
    ];
}
```

Next, add middleware to `config/nova.php`

```php
// in config/nova.php
'middleware' => [
    // ...
    \JeffersonSimaoGoncalves\NovaPermission\ForgetCachedPermissions::class,
],
```

Finally, add `MorphToMany` fields to you `app/Nova/User` resource:

```php
// ...
use Laravel\Nova\Fields\MorphToMany;

public function fields(Request $request)
{
    return [
        // ...
        MorphToMany::make('Roles', 'roles', \JeffersonSimaoGoncalves\NovaPermission\Nova\Role::class),
        MorphToMany::make('Permissions', 'permissions', \JeffersonSimaoGoncalves\NovaPermission\Nova\Permission::class),
    ];
}
```

Or if you want to attach multiple permissions at once, use `RoleBooleanGroup` and `PermissionBooleanGroup` fields (requires at least Nova 2.6.0):

```php
// ...
use JeffersonSimaoGoncalves\NovaPermission\Nova\Fields\PermissionBooleanGroup;
use JeffersonSimaoGoncalves\NovaPermission\Nova\Fields\RoleBooleanGroup;

public function fields(Request $request)
{
    return [
        // ...
        RoleBooleanGroup::make('Roles'),
        PermissionBooleanGroup::make('Permissions'),
    ];
}
```

If your `User` could have a single role at any given time, you can use `RoleSelect` field. This field will render a standard select where you can pick a single role from.

```php
// ...
use JeffersonSimaoGoncalves\NovaPermission\Nova\Fields\RoleSelect;

public function fields(Request $request)
{
    return [
        // ...
        RoleSelect::make('Role', 'roles'),
    ];
}
```

## Customization

If you want to use custom resource classes you can define them when you register a tool:

```php
// in app/Providers/NovaServiceProvider.php

// ...

public function tools()
{
    return [
        // ...
        \JeffersonSimaoGoncalves\NovaPermission\NovaPermissionTool::make()
            ->roleResource(CustomRole::class)
            ->permissionResource(CustomPermission::class),
    ];
}

```

If you want to show your roles and policies with a custom label, you can set `$labelAttribute` when instantiating your fields:

```php
// ...
use JeffersonSimaoGoncalves\NovaPermission\Nova\Fields\PermissionBooleanGroup;
use JeffersonSimaoGoncalves\NovaPermission\Nova\Fields\RoleSelect;

public function fields(Request $request)
{
    return [
        // ...
        RoleBooleanGroup::make('Roles', 'roles', null, 'description'),
        PermissionBooleanGroup::make('Permissions', 'permissions', null, 'description'),
        RoleSelect::make('Role', 'roles', null, 'description'),
    ];
}
```


## Define Policies

```php
// in app/Providers/NovaServiceProvider.php

// ...

public function tools()
{
    return [
        // ...
        \JeffersonSimaoGoncalves\NovaPermission\NovaPermissionTool::make()
            ->rolePolicy(RolePolicy::class)
            ->permissionPolicy(PermissionPolicy::class),
    ];
}

```

## Usage

A new menu item called "Permissions & Roles" will appear in your Nova app after installing this package.

## Reporting Issues

If you have a problem with this plugin or any bug, please open an issue on [GitHub](https://github.com/jeffersonsimaogoncalves/nova-permission/issues).

## Credits

This work is based on the [code by Vyuldashev](https://github.com/vyuldashev/nova-permission).
