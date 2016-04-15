RBAC Manager (Role Base Access Control ) Base on Yii 2
======================

Author
-------------
Ilham Malik Ibrahim


Documentation
-------------


- [Change Log](CHANGELOG.md).
- [Authorization Guide](http://www.yiiframework.com/doc-2.0/guide-security-authorization.html). Important, read this first before you continue.
- [Basic Usage](docs/guide/basic-usage.md).
- [Using Menu](docs/guide/using-menu.md).

Installation
------------

### Install With Composer

The preferred way to install this extension is through [composer](http://getcomposer.org/download/).

Either run

```
php composer.phar require ilhammalik/yii2-iam "~1.0"


Or, you may add

```
"ilhammalik/yii2-iam": "~1.0"
```

to the require section of your `composer.json` file and execute `php composer.phar update`.

### Install From the Archive

Download the latest release from here [releases](https://github.com/ilhammalik/yii2-iam/releases), then extract it to your project.
In your application config, add the path alias for this extension.

```php
return [
    ...
    'aliases' => [
        '@mdm/iam' => 'path/to/your/extracted',
        // for example: '@mdm/iam' => '@app/extensions/mdm/yii2-iam-2.0.0',
        ...
    ]
];
```

Usage
-----

Once the extension is installed, simply modify your application configuration as follows:

```php
return [
    'modules' => [
        'iam' => [
            'class' => 'mdm\iam\Module',
            ...
        ]
        ...
    ],
    ...
    'components' => [
        ...
        'authManager' => [
            'class' => 'yii\rbac\PhpManager', // or use 'yii\rbac\DbManager'
        ]
    ],
    'as access' => [
        'class' => 'mdm\iam\components\AccessControl',
        'allowActions' => [
            'site/*',
            'iam/*',
            'some-controller/some-action',
            // The actions listed here will be allowed to everyone including guests.
            // So, 'iam/*' should not appear here in the production, of course.
            // But in the earlier stages of your development, you may probably want to
            // add a lot of actions here until you finally completed setting up rbac,
            // otherwise you may not even take a first step.
        ]
    ],
];
```
See [Yii RBAC](http://www.yiiframework.com/doc-2.0/guide-security-authorization.html#role-based-access-control-rbac) for more detail.
You can then access Auth manager through the following URL:

```
http://localhost/path/to/index.php?r=iam
http://localhost/path/to/index.php?r=iam/route
http://localhost/path/to/index.php?r=iam/permission
http://localhost/path/to/index.php?r=iam/menu
http://localhost/path/to/index.php?r=iam/role
http://localhost/path/to/index.php?r=iam/assignment
```

To use the menu manager (optional), execute the migration here:
```
yii migrate --migrationPath=@ilhammalik/iam/migrations
```

If you use database (class 'yii\rbac\DbManager') to save rbac data, execute the migration here:
```
yii migrate --migrationPath=@yii/rbac/migrations
```

Customizing Assignment Controller
---------------------------------

Assignment controller properties may need to be adjusted to the User model of your app.
To do that, change them via `controllerMap` property. For example:

```php
    'modules' => [
        'iam' => [
            ...
            'controllerMap' => [
                 'assignment' => [
                    'class' => 'mdm\iam\controllers\AssignmentController',
                    /* 'userClassName' => 'app\models\User', */
                    'idField' => 'user_id',
                    'usernameField' => 'username',
                    'fullnameField' => 'profile.full_name',
                    'extraColumns' => [
                        [
                            'attribute' => 'full_name',
                            'label' => 'Full Name',
                            'value' => function($model, $key, $index, $column) {
                                return $model->profile->full_name;
                            },
                        ],
                        [
                            'attribute' => 'dept_name',
                            'label' => 'Department',
                            'value' => function($model, $key, $index, $column) {
                                return $model->profile->dept->name;
                            },
                        ],
                        [
                            'attribute' => 'post_name',
                            'label' => 'Post',
                            'value' => function($model, $key, $index, $column) {
                                return $model->profile->post->name;
                            },
                        ],
                    ],
                    'searchClass' => 'app\models\UserSearch'
                ],
            ],
            ...
        ]
        ...
    ],

```


