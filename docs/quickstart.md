# Quick Start

This guide will lead you through install and setting up a data table displaying users while using
as much of the core Glint functionality as possible.

```php
<?php

use Glint\Glint\DataTable;

class UsersTable extends DataTable
{
    public function getQuery()
    {
        return User::all();
    }

    public function getColumns(): array
    {
        return [

        ];
    }
}
```
