# 如何在 Laravel 中加密大文件

> 原文：<https://betterprogramming.pub/how-to-encrypt-large-files-in-laravel-293460836ded>

## 加密文件既简单又非常重要

![](img/57c4da901aadd694288d9ca946ddb64d.png)

由[马克西姆·卡哈里茨基](https://unsplash.com/@qwitka?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/file?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄的照片

最近，我遇到了一个我认为很常见的问题。在 Laravel 项目中，用户可以上传任意大小的文件，出于安全目的，文件需要在静态下加密。

Laravel 提供了[加密](https://laravel.com/docs/master/encryption)工具，但是它们主要是为加密值而设计的。用`encrypt` helper 方法加密像图像这样的小文件效果很好，但是在这个过程中，文件内容需要加载到内存中，这对于大文件来说是一个问题。

我曾经搜索过这个问题的包或者解决方案，碰到过[这个栈溢出答案](https://stackoverflow.com/questions/30742390/encrypting-large-files-in-php-with-openssl)和[这个 PHP 解决方案](https://riptutorial.com/php/example/25499/symmetric-encryption-and-decryption-of-large-files-with-openssl)，基本上就是所描述的栈溢出解决方案的 PHP 实现。

我决定创建一个为 Laravel 设计的包，使用简单、优雅的语法提供简单的文件加密/解密工具。

我已经调用了包 *FileVault* ，你可以[在 GitHub](https://github.com/soarecostin/file-vault) 上看到。如果你想跳过这个教程，你可以直接进入 GitHub repo 并开始使用这个包。该软件包包括如何使用它的详细文档。

# 辅导的

在本教程中，我将描述将大文件加密到一个全新的 Laravel 项目中所需的所有步骤。

首先，[使用 Laravel 安装程序创建一个新的 Laravel 项目](https://laravel.com/docs/6.x#installing-laravel)。我们称之为`security-app`:

```
laravel new security-app
```

在写这篇教程的时候，我们使用的是 Laravel v6.5.2。

因为我们已经使用了 Laravel 安装程序，所以我们已经生成了一个应用程序密钥并添加到了我们的`.env`文件中。如果您使用其他安装方法，请不要忘记使用以下方法生成新的应用密钥:

```
 php artisan key:generate
```

因为我们正在使用 [Laravel Valet](https://laravel.com/docs/6.x/valet) ，我们应该已经有了为我们创建的`security-app.test` 域。如果您正在使用另一个开发环境，您应该添加一个指向新项目的本地域。

由于从 v6 开始前端脚手架已经移到 Laravel UI，我们将安装`laravel/ui`包。

```
composer require laravel/ui — dev
```

接下来，我们将安装`bootstrap`和`auth`脚手架:

```
php artisan ui bootstrap --auth
```

编译所有的东西。

```
npm install && npm run dev
```

我们还需要在`.env`文件中配置我们的数据库访问凭证，并运行初始迁移:

```
php artisan migrate
```

我们现在可以创建一个新用户并登录以查看用户控制面板。

注意:出于本演示的目的，我们将创建一个基本的上传表单，但是在您的应用程序中，您应该考虑使用更复杂的上传功能，对大文件使用分块上传。

你可以使用的一个非常好的包是[pion/laravel-chunk-upload](https://packagist.org/packages/pion/laravel-chunk-upload)。

Laravel auth scaffolding 为我们创建了一个`/home`路线、`HomeController`、和`home.blade.php`视图文件。

让我们编辑`home.blade.php`文件并添加一个表单和一个上传字段:

接下来，我们将添加一条新路线:

```
Route::post(‘/home’, ‘HomeController@store’)->name(‘uploadFile’);
```

和一个新的`store`方法到`HomeController`到*。*该方法会将上传的文件存储在`files`目录(`storage/app/files/{user-id}`)下的一个子目录中，该子目录具有当前用户 ID。

注意:这是一种糟糕的做法，不应该在生产应用程序中使用。我们依靠文件系统来获取用户的文件，以用于演示目的，从而使本教程保持简短，但是在生产应用程序中需要一个更健壮的系统，使用数据库表来跟踪每个用户的文件。

这是我们需要*加密用户上传文件的阶段。*我们将拉入[文件库](https://github.com/soarecostin/file-vault)包:

```
composer require soarecostin/file-vault
```

这个包允许访问 *FileVault facade* ，它公开了一些加密和解密文件的方法，以及一些设置选项的方法，比如为每个文件设置不同的加密密钥，或者指定文件所属的 Laravel 文件系统磁盘。

我们将使用`FileVault::encrypt($file)`方法来加密用户上传的文件。此功能将删除原来未加密的文件，并替换为具有相同名称和附加`.enc` 扩展名的文件。

如果你想用不同的名字命名你的文件，你可以把你想要的名字作为第二个参数传递给`encrypt`方法。如果你想保存你的原始文件，你可以使用`encryptCopy`方法。

这就是我们现在的`store` 法的样子:

接下来，我们需要看到所有用户上传的文件，我们还需要一种下载它们的方法。

我们将在`HomeController`中创建新的`downloadFile`路线和新的`downloadFile`方法:

```
Route::get(‘/files/{filename}’, ‘HomeController@downloadFile’)->name(‘downloadFile’);
```

`downloadFile` 方法使用 Laravel 本机 [*streamDownload 响应*](https://laravel.com/docs/5.8/responses#file-downloads) ，它接受回调。

在回调中，我们调用包*、*提供的 *FileVault facade* 的`streamDecrypt`方法，它将解密文件并逐段提供给`streamDownload`方法，允许您的用户直接下载解密后的文件。

我们现在需要在上传表单下面显示所有用户的文件。为此，我们将从`HomeController`的`index`方法向`home.blade.php`视图文件发送一个`$files`变量，并在上传表单下方的`home.blade.php`文件中显示用户的文件。

就是这个！我们现在使用静态加密！我们已经为用户创建了一个上传文件的表单，我们正在对这些文件进行加密，只有在上传文件的用户请求时，我们才会对它们进行解密。

当然，在生产中，需要更多的安全措施，FileVault 包就是为了在这方面帮助您而设计的。

例如，您可能希望将用户上传的大文件存储在亚马逊 S3 的[中，该软件包支持对文件进行加密/流解密。](https://aws.amazon.com/s3/)

您可能还希望对每个用户或每个文件使用不同的加密密钥，这也可以通过 FileVault 包实现。

# 资源

*   你可以在 [this GitHub repo](https://github.com/soarecostin/file-encryption-tutorial) 中找到本教程中创建的整个 Laravel 应用。
*   如果你喜欢这个教程，并想了解更多关于如何使用亚马逊 S3 上传和加密文件，你可以访问我写的另一篇文章， [*如何在 Laravel*](https://medium.com/@soarecostin/how-to-encrypt-upload-large-files-to-amazon-s3-in-laravel-af88324a9aa?sk=a9a358a3892e898a60448d5314fb3dc0) 加密和上传大文件到亚马逊 S3。