---
title: WordPress上传文件类型限制解决办法
comments: true
categories:
  - server
  - wordpress
tags:
  - config
abbrlink: fd16a479
date: 2019-04-04 11:31:30
---

# WordPress上传文件类型限制解决办法

现象：文件类型不符合安全规则。试试别的文件。

这种错误是由于WordPress中做了文件上传格式的限制，这种限制可以在WordPress中的`wp-include/functions.php`的`get_allowed_mime_types`函数中找到，函数如下：

```php
function get_allowed_mime_types() {
static $mimes = false;
if ( !$mimes ) {
// Accepted MIME types are set here as PCRE unless provided.
$mimes = apply_filters( ‘upload_mimes’, array(
‘jpg|jpeg|jpe’ => ‘image/jpeg’,
‘gif’ => ‘image/gif’,
‘png’ => ‘image/png’,
‘bmp’ => ‘image/bmp’,
‘tif|tiff’ => ‘image/tiff’,
‘ico’ => ‘image/x-icon’,
‘asf|asx|wax|wmv|wmx’ => ‘video/asf’,
‘avi’ => ‘video/avi’,
‘divx’ => ‘video/divx’,
‘flv’ => ‘video/x-flv’,
‘mov|qt’ => ‘video/quicktime’,
‘mpeg|mpg|mpe’ => ‘video/mpeg’,
‘txt|c|cc|h’ => ‘text/plain’,
‘rtx’ => ‘text/richtext’,
‘css’ => ‘text/css’,
‘htm|html’ => ‘text/html’,
‘mp3|m4a’ => ‘audio/mpeg’,
‘mp4|m4v’ => ‘video/mp4′,
‘ra|ram’ => ‘audio/x-realaudio’,
‘wav’ => ‘audio/wav’,
‘ogg’ => ‘audio/ogg’,
‘mid|midi’ => ‘audio/midi’,
‘wma’ => ‘audio/wma’,
‘rtf’ => ‘application/rtf’,
‘js’ => ‘application/javascript’,
‘pdf’ => ‘application/pdf’,
‘doc|docx’ => ‘application/msword’,
‘pot|pps|ppt|pptx’ => ‘application/vnd.ms-powerpoint’,
‘wri’ => ‘application/vnd.ms-write’,
‘xla|xls|xlsx|xlt|xlw’ => ‘application/vnd.ms-excel’,
‘mdb’ => ‘application/vnd.ms-access’,
‘mpp’ => ‘application/vnd.ms-project’,
’swf’ => ‘application/x-shockwave-flash’,
‘class’ => ‘application/java’,
‘tar’ => ‘application/x-tar’,
‘zip’ => ‘application/zip’,
‘gz|gzip’ => ‘application/x-gzip’,
‘exe’ => ‘application/x-msdownload’,
// openoffice formats
‘odt’ => ‘application/vnd.oasis.opendocument.text’,
‘odp’ => ‘application/vnd.oasis.opendocument.presentation’,
‘ods’ => ‘application/vnd.oasis.opendocument.spreadsheet’,
‘odg’ => ‘application/vnd.oasis.opendocument.graphics’,
‘odc’ => ‘application/vnd.oasis.opendocument.chart’,
‘odb’ => ‘application/vnd.oasis.opendocument.database’,
‘odf’ => ‘application/vnd.oasis.opendocument.formula’,
) );
}
return $mimes;
}
```

以上传入`apply_filters`函数中的Array，即使允许上传的类型列表，我们可以修改这个地方，以禁止或允许可以上传的文件类型。  
例如我们想要允许rar文件上传，只需添加一行格式说明即可：

```php
‘rar’ => ‘application/rar’,
```

除了直接修改这个Array外，还有另外一种方法，可以修改`wp-config.php`文件，这个文件在WordPress的根目录下，在其中增加一行语句：

```php
define(‘ALLOW_UNFILTERED_UPLOADS’, true);
```

这条语句将去除上传的文件类型限制，一劳永逸。