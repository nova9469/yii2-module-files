# yii2-module-files

[![Build Status](https://travis-ci.org/floor12/yii2-module-files.svg?branch=master)](https://travis-ci.org/floor12/yii2-module-files)
[![Latest Stable Version](https://poser.pugx.org/floor12/yii2-module-files/v/stable)](https://packagist.org/packages/floor12/yii2-module-files)
[![Latest Unstable Version](https://poser.pugx.org/floor12/yii2-module-files/v/unstable)](https://packagist.org/packages/floor12/yii2-module-files)
[![Total Downloads](https://poser.pugx.org/floor12/yii2-module-files/downloads)](https://packagist.org/packages/floor12/yii2-module-files)
[![License](https://poser.pugx.org/floor12/yii2-module-files/license)](https://packagist.org/packages/floor12/yii2-module-files)

*Этот файл доступен на [русском языке](README_RU.md).*
 
This module allows to add files attributes to your ActiveRecord Models.

This module includes widgets for ActiveForm to upload, crop and edit files, and also widget to show files in frontend. 

Installation
------------

#### Installation of module in you app

Just run:
```bash
$ composer require floor12/yii2-module-files
```

or add this to the require section of your composer.json.
```json
"floor12/yii2-module-files": "dev-master"
```


Run the migration to create a table for `File` model:
```bash
$ ./yii migrate --migrationPath=@vendor/floor12/yii2-module-files/src/migrations/
```

Add this to **modules** section
```php  
'modules' => [
            'files' => [
                'class' => 'floor12\files\Module',
                'storage' => '@vendor/../storage',
                'token_salt' => '!FgGGsdfsef2ad3@Ejhfskj34',
            ],
        ],
    ...
```

Params:

- `storage` - path alias to folder where files must be stored. Default is *storage* folder in root of your app.
- `token_salt` - unique salt to protect file edit forms.


Usage
-----

### Work with the ActiveRecord model

To connect the module to the `ActiveRecord` model, you must assign it a `FileBehaviour`
and specify the attributes parameter, what fields with files need to be created:

```php 
 public function behaviors()
 {
     return [
         'files' => [
             'class' => 'floor12\files\components\FileBehaviour',
             'attributes' => [
                 'avatar',
                 'documents'
             ],
         ],
         ...
```

As for the other attributes of the model, specify the `attributeLabels()`:

```php 
 public function attributeLabels()
    {
        return [
            'avatar' => 'User avatar',
            'documents' => 'Attachments',
        ];
    }
```

 Specify the the validation `rules()`:
```php
public function rules()
{
    return [
        ['avatar', 'required],
        ['avatar', 'file', 'extensions' => ['jpg', 'png', 'jpeg', 'gif'], 'maxFiles' => 1], 
        ['docs', 'file', 'extensions' => ['docx','xlsx'], 'maxFiles' => 10],
    ...    
```

In case when `'maxFiles' => 1`, model attribute will contain a single `floor12\files\models\File` object.
For example:
```php
echo Html::img($model->avatar->href)            // the web path to file
echo Html::img($model->avatar->hrefPreview)     // the  web path to file preview, if the file is image
echo Html::img($model->avatar)                  // __toString of File returns the web path
```

If `maxFiles` has 2 or more (multiple file upload), model attribute will contain an array of `floor12\files\models\File` objects.
-> avatar. 

```php
foreach ($model->docs as $doc}
    Html::a($doc->title, $doc->href);
```
### Widget to list all files
In addition, there is a widget for displaying all files, which makes it possible to view images 
in the [Lightbox2](https://lokeshdhakar.com/projects/lightbox2/) gallery and preview files in Office Online. 
It is also possible to download all the files attached to current attribute archived in ZIP format.

 ```php
echo \floor12\files\components\FilesListWidget::widget([
    'files' => $model->docs, 
    'title' => 'Attachments:',            // by default Label from model will used 
    'downloadAll' => true, 
    'zipTitle' => "docs_of_model_" . $model->id
]) 
```

### Widget for ActiveFrom

Use special widget to upload and reorder (both with drug-and-drop), crop and rename files in forms.

```php
    <?= $form->field($model, 'avatar')->widget(FileInputWidget::class, []) ?>
    
    <?= $form->field($model, 'docs')->widget(FileInputWidget::class, []) ?>
```
The widget itself will take the desired form, in the case of adding 1 or more files.

