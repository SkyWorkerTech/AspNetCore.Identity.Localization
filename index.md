# Asp.Net Core 2.2 MVC + Identity 正體中文化 / 多國語系 範例程式

[![Build Status](https://skyworkertech.visualstudio.com/jayskyworker/_apis/build/status/JaySkyworker.AspNetCore.Identity.Localization?branchName=master)](https://skyworkertech.visualstudio.com/jayskyworker/_build/latest?definitionId=1&branchName=master)

Asp.net Core 2.2 MVC 在使用預設英文的情況下，可以快速建立預設應用程式。
但是一旦你想要正體中文化 / 多國語系，你會發現許多預設錯誤訊息等等，需要大量的程式改寫
，及無法使用共用的 資源。

這範例程式 提供一個比較完整的解決方案，特別是 錯誤訊息的 共用部分。

希望能幫助到你!

> **注意**: 不是所有介面文字都有中文翻譯!
> 
> 一些比較不常用的View/Page 並沒有翻譯, 請依照這些範例自行補足你需要的部分.

## 一般 Localization

請具備 Localization 的一般知識。

https://docs.microsoft.com/zh-tw/aspnet/core/fundamentals/localization?view=aspnetcore-2.2

## Localization 基本設定

參考這個  [commit](https://github.com/JaySkyworker/AspNetCore.Identity.Localization/commit/a4bd2312f0a14090b6f74d667888822cf32dd93b).

- 在 `Starup.cs`
  - 增加 `RequestLocalizationOptions` 及 `supportedCultures`.
  - 增加 `app.UseRequestLocalization()`
- 增加 `_SelectLanguagePartial.cshtml` 及 `SetLanguageController.cs` 讓介面有一個簡易的語系選擇 下拉選單。


## View / Page / Controller Localization

一般性文字 有許多種 方法 讓他可以多國語系，我選擇原始英文當作 Resources 的 Key ，
這樣即使找不到對應的語系也會呈現英文。

參考這個 [commit](https://github.com/JaySkyworker/AspNetCore.Identity.Localization/commit/989fce76be2bcf71862976c5a2b3b2459e533af9)
 及這個[commit](https://github.com/JaySkyworker/AspNetCore.Identity.Localization/commit/ba281e355c426634b022f050c0b593618874ffb2)
.

參考:

* https://damienbod.com/2018/07/03/adding-localization-to-the-asp-net-core-identity-pages/

* https://github.com/damienbod/AspNetCorePagesWebpack

## Model / DataAnnotation Localization

Models 及 DataAnnotations 中 有設定文字的，例如：
```csharp
    [Display(Name = "Confirm password")]
    [Compare("Password", ErrorMessage = "The password and confirmation password do not match.")]
    public string ConfirmPassword { get; set; }
```
使用 `DataAnnotationsLocalization` 來達成多國語系， 
但是 這對 預設 的 display name 跟 ErrorMessages 並無作用，
我在下面章節另外做。


參考這個 [commit](https://github.com/JaySkyworker/AspNetCore.Identity.Localization/commit/7c706dab8494c118f0a4c8e7d7522ba705a7467f).

- 在 `Starup.cs`
  - 在 `AddMvc()` 之後，增加 `.AddDataAnnotationsLocalization()` 
- 增加 `DataAnnotationSharedResource.resx`.

### ConventionalMetadataProviders

```csharp
    [Required]
    [EmailAddress]
    public string Email { get; set; }
```
`Email` 預設的名稱 Display name 及
`[Required]`, `[EmailAddress]` 等等 的預設錯誤訊息 使用 `.ModelMetadataDetailsProviders` 來達成多國語系。

參考這個[commit](https://github.com/JaySkyworker/AspNetCore.Identity.Localization/commit/326b72d08d83fd7ef8b52795c63a55cf5d5eaa22)
及這個 [commit](https://github.com/JaySkyworker/AspNetCore.Identity.Localization/commit/20d2b24716c3eb194d9de68484d939e27bf5c2bc)
.

- 在 `Starup.cs`
  - 增加 `ConventionalDisplayMetadataProvider`, `ConventionalValidationMetadataProvider`  跟其他 相關的 classes.
  - 在 Mvc Options, 增加 `options.SetConventionalMetadataProviders()` .
- 增加 `ValidationMetadataSharedResource.resx` 來對應 驗證失敗的 ErrorMessage.
- 增加`DisplayMetadataSharedResource.resx` 來對應 Display name.

參考:
* https://github.com/ridercz/Altairis.ConventionalMetadataProviders

#### ConventionalDisplayMetadataProvider

預設 Display name 的多國語系, 這個 provider 依照習慣的慣例 ( conventions ) 會自動尋找合適的 resource key，依照下方的順序:

> [[Namespace_]TypeName_]PropertyName

這個 provider 會依照 精準度 高 到 低 依序尋找對應的 resource key， 
一旦找到就會停止：

- `App_Areas_Identity_Pages_Account_LoginModel_InputModel_Email`
- `Areas_Identity_Pages_Account_LoginModel_InputModel_Email`
- `Identity_Pages_Account_LoginModel_InputModel_Email`
- ...
- `LoginModel_InputModel_Email`
- `InputModel_Email`
- `Email`

#### ConventionalValidationMetadataProvider

預設的 ErrorMessages 的多國語系, 跟規則跟前述一樣:

> [[[Namespace_]TypeName_]PropertyName_]ValidatorType

這個 provider 會依照 精準度 高 到 低 依序尋找對應的 resource key， 
一旦找到就會停止：

- `App_Areas_Identity_Pages_Account_LoginModel_InputModel_Email_Required`
- `Areas_Identity_Pages_Account_LoginModel_InputModel_Email_Required`
- `Identity_Pages_Account_LoginModel_InputModel_Email_Required`
- ...
- `InputModel_Email_Required`
- `Email_Required`
- `Required`

你應該為各個 validation type 保留一個 預設的 ErrorMessages.
查看 [DefaultValidationMessages.zh-TW.resx](https://github.com/JaySkyworker/AspNetCore.Identity.Localization/blob/feature/ConventionalMetadataProviders/Resources/DefaultValidationMessages.zh-TW.resx)

## Identity 相關驗證 錯誤訊息的 Localization

Identity 會驗證 密碼等等的設定. 我們需要為這些錯誤訊息做多國語系。

參考這個 [commit](https://github.com/JaySkyworker/AspNetCore.Identity.Localization/commit/bae9cab2ac5684aa720de15adf379c8e7f4b46cc).

- 增加 `LocalizedIdentityErrorDescriber.cs` and `LocalizedIdentityErrorMessages.resx`.
- 在 `Starup.cs`
  - 在 `AddDefaultIdentity<IdentityUser>()` 之後，增加 `.AddErrorDescriber<LocalizedIdentityErrorDescriber>()`.

參考:

* http://www.ziyad.info/en/articles/20-Localizing_Identity_Error_Messages

* https://stackoverflow.com/questions/19961648/how-to-localize-asp-net-identity-username-and-password-error-messages


## 如何提供翻譯
歡迎你提供其他多國語系的翻譯!

**如何提交新的翻譯:**

1. Fork the repo.
1. 新增你要翻譯的語系的 resource 檔案.
1. 翻譯它 ( ^_^ ).
1. 在`Startup.cs` 的 `supportedCultures` 清單中 新增你的語系，在 `zu-ZA` 之前.
1. 開一個 pull request.

我會盡快處理，謝謝.

