# Quick Start

This guide will lead you through install and setting up a data table displaying users while using
as much of the core Glint functionality as possible.

Start by creating a file `touch app\Livewire\UserDataTable.php`. Glint works by implementing
methods to define the model query, header actions, columns, row actions, filtering etc. The API is
fairly similar to Filament.

_Note: Please ignore the repeated use of `<?php`. It is included entirely to trigger the correct
syntax highlighting rules for MkDocs._

```php
<?php

use Glint\Glint\DataTable;

class UsersTable extends DataTable
{
public array $perPageOption = [50, 100];

public int $perPage = 50;

public ?string $sortColumn = 'last_name';
}
```

## Querying Data

To define the builder query, override the `getQuerty()` method. Any query will work. We recommend
using eager loading for performance.

```php
<?php
public function getQuery()
{
    return User::query()
        ->with([
            'profile',
            'friends'
        ])
        ->where('is_active', true);
}
```

## Defining Columns

Implement the `getColumns` method to define the columns you want in your table. Current, Glint
supports `TextColumn`, `DateColumn`, and `TagColumn`. By default, column labels are a headlined
version of the attribute name.

```php
<?php
public function getColumns()
{
    return [
        TextColumn::make('name')
            ->sortable()
            ->searchable()
        ,
        DateColumn('created_at')
            ->label('Creation date')
            ->format('m/d/Y'),
        TextColumn('friends.name')
            ->label('Number of friends')
            ->getStateUsing(fn ($record) => $record->friends->count()),
        TagColumn('is_admin')
    ];
}
```

## Filtering Tables
Table filters can be defined by implementing `getFilters()` which returns an array of filter types.
Currently, you can use `CheckboxFilter`, `DateFilter`, or `SelectFilter`.

```php
<?php
public function getFilters(): array
{
    return [
        CheckBoxFilter('is_admin')
            ->label('Admin Only')
            ->queryUsing(fn ($query, $value)) => $value ? $query->where('is_admin', true) : $query)
    ];
}
```

## Row Actions

You can define an action columns that will show a drop down of actiosn that can be performed on
that row. For example, if you want to allow users to be able to delete the user, can you implement
`getRowActions()` with a `DeleteAction`.

```php
<?php
public function getRowActions(): array
{
    return [
        DeleteAction::make('Remove User')
    ];
}
```

## Header Actions
Actions can be defined at the table level. For example, if you want a button in the table before
the rows to add a user, you can implement `getHeaderActions` and return a `LinkAction`. While we
are at it, we will also add an export button.

```php
<?php
public function getHeaderActions(): array
{
    return [
        LinkAction::make('Add User')
            ->url(route('user.new')),
        ExportAction::make(),
    ];
}
```
