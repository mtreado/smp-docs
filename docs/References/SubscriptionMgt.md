# SubscriptionMgt_SM_TSL (Codeunit)
SubscriptionMgt_SM_TSL codeunit contains all the code blocks for the integration aspect of the Subscription Management app. It comprises multiple functions as described below. 
Before progressing, please ensure that the Subscription Management extension is installed into your development environment and the right dependency has been defined in your extension's `app.json`: 
```json
"dependencies": [
   ...
   {
      "id": "6717135a-d80c-4a63-8a3a-5ded6717135a",
      "publisher": "Theta Systems Limited",
      "name": "SubscriptionMgt",
      "version": "1.1.1.0"
    }
]
```
## Common Parameters
| Name | Type | Description |
| - | - | - |
| SecretKey | Text | Your [Stripe API Secret](https://stripe.com/docs/keys#obtain-api-keys)<br> **This key can perform any API request to Stripe without restriction, we highly recommend you leverage a [secure vault](https://docs.microsoft.com/en-us/dynamics365/business-central/dev-itpro/developer/devenv-app-key-vault-overview) to safeguard the integrity of your account.** |
| PublishableKey | Text | Your [Stripe Publishable Key](https://stripe.com/docs/keys#obtain-api-keys)<br> *This key exists solely to identify your account with Stripe and can be considered a public identified key that can safely remain in source.* |
| ProductID | Text\[100\] | Your [Stripe Product Identifier](https://dashboard.stripe.com/products)<br> The identifier of the product associated with your extension. |
<!-- theme: info -->
> _SecretKey_, _PublishableKey_ and _ProductID_ values differ across your Stripe account between your test and live modes. Use test values during development/testing and make sure you use your live key in your production-ready package. The easiest way to do this is by replacing the [keyVaultUrls](https://docs.microsoft.com/en-us/dynamics365/business-central/dev-itpro/developer/devenv-app-key-vault#specify-the-azure-key-vault-in-extensions) values within your continuous integration process.

## Integration API
### TryAddProduct (Method)
Requests Subscription Management to start handling your extension. It's recommended to call it with `OnInstallAppPerDatabase` and `OnUpgradePerDatabase` events.
#### TryAddProduct(Text,Text,ModuleInfo,Text[100])
![](https://img.shields.io/badge/version-v1.0.0.0-blue)
```sql
[NonDebuggable]
TryAddProduct(SecretKey:Text;PublishableKey:Text;Info:ModuleInfo;ProductID:Text[100]):Boolean
```
##### Parameters
| Name | Type | Description |
| - | - | - |
| SecretKey | Text | [Stripe Secret Key](#common-parameters) |
| PublishableKey | Text | [Stripe Publishable Key](#common-parameters) |
| Info | ModuleInfo | It's your extension [module info](https://docs.microsoft.com/en-us/dynamics365/business-central/dev-itpro/developer/methods-auto/moduleinfo) object instance. It can be retrieved by using `NavApp.GetCurrentModuleInfo(Info)` function. |
| ProductID | Text\[100\] | [Stripe Product ID](#common-parameters) |
##### Returns
| Type | Description |
| - | - |
| Boolean | Indicating whether the operation completed successfully |
##### Examples
<!--
type: tab
title: InstallExt.Codeunit.al
-->
```sql
codeunit 50000 InstallExt
{
    Subtype = Install;

    trigger OnInstallAppPerDatabase()
    var
        SecretProvider: Codeunit "App Key Vault Secret Provider";
        SubscriptionMgt: Codeunit SubscriptionMgt_SM_TSL;
        Info: ModuleInfo;
        [NonDebuggable]
        SecretKey: Text;
        [NonDebuggable]
        PublishableKey: Text;
        ProductID: Text[100];
    begin
        if NavApp.GetCurrentModuleInfo(Info) then
            if SecretProvider.TryInitializeFromCurrentApp() then
                if SecretProvider.GetSecret('SecretKey', SecretKey) and
                   SecretProvider.GetSecret('PublishableKey', PublishableKey) and
                   SecretProvider.GetSecret('MyAppProductID', ProductID)
                then
                    SubscriptionMgt.TryAddProduct(
                        SecretKey,
                        PublishableKey,
                        Info,
                        ProductID);
    end;
}
```
<!--
type: tab
title: UpgradeExt.Codeunit.al
-->
```sql
codeunit 50001 UpgradeExt
{
    Subtype = Upgrade;

    trigger OnUpgradePerDatabase()
    var
        SecretProvider: Codeunit "App Key Vault Secret Provider";
        SubscriptionMgt: Codeunit SubscriptionMgt_SM_TSL;
        Info: ModuleInfo;
        [NonDebuggable]
        SecretKey: Text;
        [NonDebuggable]
        PublishableKey: Text;
        ProductID: Text[100];
    begin
        if NavApp.GetCurrentModuleInfo(Info) then
            if SecretProvider.TryInitializeFromCurrentApp() then
                if SecretProvider.GetSecret('SecretKey', SecretKey) and
                   SecretProvider.GetSecret('PublishableKey', PublishableKey) and
                   SecretProvider.GetSecret('MyAppProductID', ProductID)
                then
                    SubscriptionMgt.TryAddProduct(
                        SecretKey,
                        PublishableKey,
                        Info,
                        ProductID);
    end;
}
```
<!-- type: tab-end -->
#### TryAddProduct(Text,Text,Guid,Text[100])
![](https://img.shields.io/badge/version-v1.0.0.0-blue)
```sql
[NonDebuggable]
TryAddProduct(SecretKey:Text;PublishableKey:Text;AppID:Guid;ProductID:Text[100]):Boolean
```
##### Parameters
| Name | Type | Description |
| - | - | - |
| SecretKey | Text | [Stripe Secret Key](#common-parameters) |
| PublishableKey | Text | [Stripe Publishable Key](#common-parameters) |
| AppID | Guid | The [unique ID of your extension](https://docs.microsoft.com/en-us/dynamics365/business-central/dev-itpro/developer/devenv-json-files#Appjson). You can retrieve it from [current module info](https://docs.microsoft.com/en-us/dynamics365/business-central/dev-itpro/developer/methods-auto/moduleinfo/moduleinfo-id-method). |
| ProductID | Text\[100\] | [Stripe Product ID](#common-parameters) |
##### Returns
| Type | Description |
| - | - |
| Boolean | Indicating whether the operation completed successfully |
##### Examples
<!--
type: tab
title: InstallExt.Codeunit.al
-->
```sql
codeunit 50000 InstallExt
{
    Subtype = Install;

    trigger OnInstallAppPerDatabase()
    var
        SecretProvider: Codeunit "App Key Vault Secret Provider";
        SubscriptionMgt: Codeunit SubscriptionMgt_SM_TSL;
        Info: ModuleInfo;
        [NonDebuggable]
        SecretKey: Text;
        [NonDebuggable]
        PublishableKey: Text;
        ProductID: Text[100];
    begin
        if NavApp.GetCurrentModuleInfo(Info) then
            if SecretProvider.TryInitializeFromCurrentApp() then
                if SecretProvider.GetSecret('SecretKey', SecretKey) and
                   SecretProvider.GetSecret('PublishableKey', PublishableKey) and
                   SecretProvider.GetSecret('MyAppProductID', ProductID)
                then
                    SubscriptionMgt.TryAddProduct(
                        SecretKey,
                        PublishableKey,
                        Info.Id(),
                        ProductID);
    end;
}
```
<!--
type: tab
title: UpgradeExt.Codeunit.al
-->
```sql
codeunit 50001 UpgradeExt
{
    Subtype = Upgrade;

    trigger OnUpgradePerDatabase()
    var
        SecretProvider: Codeunit "App Key Vault Secret Provider";
        SubscriptionMgt: Codeunit SubscriptionMgt_SM_TSL;
        Info: ModuleInfo;
        [NonDebuggable]
        SecretKey: Text;
        [NonDebuggable]
        PublishableKey: Text;
        ProductID: Text[100];
    begin
        if NavApp.GetCurrentModuleInfo(Info) then
            if SecretProvider.TryInitializeFromCurrentApp() then
                if SecretProvider.GetSecret('SecretKey', SecretKey) and
                   SecretProvider.GetSecret('PublishableKey', PublishableKey) and
                   SecretProvider.GetSecret('MyAppProductID', ProductID)
                then
                    SubscriptionMgt.TryAddProduct(
                        SecretKey,
                        PublishableKey,
                        Info.Id(),
                        ProductID);
    end;
}
```
<!-- type: tab-end -->

### IsActive (Method)
Checks whether the product subscription is in one of the active states (active, trialing, past_due, incomplete, incomplete_expired).

#### IsActive(Text,Text[100])
![](https://img.shields.io/badge/version-v1.0.0.0-blue)
```sql
[NonDebuggable]
IsActive(SecretKey:Text;ProductID:Text[100]):Boolean
```
##### Parameters
| Name | Type | Description |
| - | - | - |
| SecretKey | Text | [Stripe Secret Key](#common-parameters) |
| ProductID | Text\[100\] | [Stripe Product ID](#common-parameters) |
##### Returns
| Type | Description |
| - | - |
| Boolean | Indicates whether product subscription is in one of the active states (active or trialing) |
##### Examples
```sql
pageextension 50000 MyExtension extends "Customer Card"
{
    trigger OnOpenPage()
    begin
        CurrPage.Editable := IsActive()
    end;

    [NonDebuggable]
    procedure IsActive(): Boolean
    var
        SecretProvider: Codeunit "App Key Vault Secret Provider";
        SubscriptionMgt: Codeunit SubscriptionMgt_SM_TSL;
        SecretKey: Text;
        ProductID: Text[100];
    begin
        if SecretProvider.TryInitializeFromCurrentApp() then
            if SecretProvider.GetSecret('SecretKey', SecretKey) and
               SecretProvider.GetSecret('MyAppProductID', ProductID)
            then
                exit(SubscriptionMgt.IsActive(SecretKey, ProductID));
    end;
}
```
### IsTrialing (Method)
Returns whether a subscription is currently under a trial period.
#### IsTrialing(Text,Text[100])
![](https://img.shields.io/badge/version-v1.0.0.0-blue)
```sql
[NonDebuggable]
IsTrialing(SecretKey:Text;ProductID:Text[100]):Boolean
```
##### Parameters
| Name | Type | Description |
| - | - | - |
| SecretKey | Text | [Stripe Secret Key](#common-parameters) |
| ProductID | Text\[100\] | [Stripe Product ID](#common-parameters) |
##### Returns
| Type | Description |
| - | - |
| Boolean | Indicates whether a subscription is currently under a trial period. |
##### Examples
```sql
TBD
```
### GetPriceName (Method)
Returns name of the user selected plan. Returns `''` if no plan is selected.
#### GetPriceName(Text,Text[100])
<a href="../References/Changelog.md#1040-2020-12-13" style="border-bottom-width:0px;background:transparent;padding:0px;">![](https://img.shields.io/badge/version-v1.0.4.0-blue)</a>
```sql
[NonDebuggable]
GetPriceName(SecretKey:Text;ProductID:Text[100]):Text
```
##### Parameters
| Name | Type | Description |
| - | - | - |
| SecretKey | Text | [Stripe Secret Key](#common-parameters) |
| ProductID | Text\[100\] | [Stripe Product ID](#common-parameters) |
##### Returns
| Type | Description |
| - | - |
| Text | TBD |
##### Examples
```sql
TBD
```
### CountProductLines (Method)
Returns the current quantity of the specified product. Default return value is `1` if the quantity could not be found.
#### CountProductLines(Text,Text[100])
![](https://img.shields.io/badge/version-Unreleased-blue)
```sql
[NonDebuggable]
CountProductLines(SecretKey:Text;ProductID:Text[100]):BigInteger
```
##### Parameters
| Name | Type | Description |
| - | - | - |
| SecretKey | Text | [Stripe Secret Key](#common-parameters) |
| ProductID | Text\[100\] | [Stripe Product ID](#common-parameters) |
##### Returns
| Type | Description |
| - | - |
| BigInteger | Current quantity of the product |
##### Examples
```sql
TBD
```
#### CountProductLines(Text,Text[100],Enum)
![](https://img.shields.io/badge/version-Unreleased-blue)
```sql
[NonDebuggable]
CountProductLines(SecretKey:Text;ProductID:Text[100];Scope:Enum):BigInteger
```
##### Parameters
| Name | Type | Description |
| - | - | - |
| SecretKey | Text | [Stripe Secret Key](#common-parameters) |
| ProductID | Text\[100\] | [Stripe Product ID](#common-parameters) |
| Scope | Enum | TBD |
##### Returns
| Type | Description |
| - | - |
| BigInteger | Current quantity of the product |
##### Examples
```sql
TBD
```
### GetProductLines (Method)
Returns the list of current quantity lines. 
#### GetProductLines(Text,Text[100],var Dictionary)
![](https://img.shields.io/badge/version-Unreleased-blue)
```sql
[NonDebuggable]
GetProductLines(SecretKey:Text;ProductID:Text[100];var Lines:Dictionary)
```
##### Parameters
| Name | Type | Description |
| - | - | - |
| SecretKey | Text | [Stripe Secret Key](#common-parameters) |
| ProductID | Text\[100\] | [Stripe Product ID](#common-parameters) |
| Lines | Dictionary | TBD |
##### Examples
```sql
TBD
```
#### GetProductLines(Text,Text[100],Enum,var Dictionary)
![](https://img.shields.io/badge/version-Unreleased-blue)
```sql
[NonDebuggable]
GetProductLines(SecretKey:Text;ProductID:Text[100];Scope:Enum;var Lines:Dictionary))
```
##### Parameters
| Name | Type | Description |
| - | - | - |
| SecretKey | Text | [Stripe Secret Key](#common-parameters) |
| ProductID | Text\[100\] | [Stripe Product ID](#common-parameters) |
| Scope | Enum | TBD |
| Lines | Dictionary | TBD |
##### Examples
```sql
TBD
```
### SetProductLines (Method)
Updates the list of current quantity lines. 
#### SetProductLines(Text,Text[100],Dictionary)
![](https://img.shields.io/badge/version-Unreleased-blue)
```sql
[NonDebuggable]
SetProductLines(SecretKey:Text;ProductID:Text[100];var Lines:Dictionary)
```
##### Parameters
| Name | Type | Description |
| - | - | - |
| SecretKey | Text | [Stripe Secret Key](#common-parameters) |
| ProductID | Text\[100\] | [Stripe Product ID](#common-parameters) |
| Lines | Dictionary | TBD |
##### Examples
```sql
TBD
```
#### SetProductLines(Text,Text[100],Enum,Dictionary)
![](https://img.shields.io/badge/version-Unreleased-blue)
```sql
[NonDebuggable]
SetProductLines(SecretKey:Text;ProductID:Text[100];Scope:Enum;var Lines:Dictionary)
```
##### Parameters
| Name | Type | Description |
| - | - | - |
| SecretKey | Text | [Stripe Secret Key](#common-parameters) |
| ProductID | Text\[100\] | [Stripe Product ID](#common-parameters) |
| Scope | Enum | TBD |
| Lines | Dictionary | TBD |
##### Examples
```sql
TBD
```
#### SetProductLines(Text,Text[100],Boolean,Dictionary)
![](https://img.shields.io/badge/version-Unreleased-blue)
```sql
[NonDebuggable]
SetProductLines(SecretKey:Text;ProductID:Text[100];ResetBillingCycle:Boolean;var Lines:Dictionary)
```
##### Parameters
| Name | Type | Description |
| - | - | - |
| SecretKey | Text | [Stripe Secret Key](#common-parameters) |
| ProductID | Text\[100\] | [Stripe Product ID](#common-parameters) |
| ResetBillingCycle | Boolean | if `true`, it resets the subscription’s billing cycle anchor to the current time. If `false`, the quantity changes will be applied to a next invoice date. |
| Lines | Dictionary | TBD |
##### Examples
```sql
TBD
```
#### SetProductLines(Text,Text[100],Enum,Boolean,Dictionary)
![](https://img.shields.io/badge/version-Unreleased-blue)
```sql
[NonDebuggable]
SetProductLines(SecretKey:Text;ProductID:Text[100];Scope:Enum;ResetBillingCycle:Boolean;var Lines:Dictionary)
```
##### Parameters
| Name | Type | Description |
| - | - | - |
| SecretKey | Text | [Stripe Secret Key](#common-parameters) |
| ProductID | Text\[100\] | [Stripe Product ID](#common-parameters) |
| Scope | Enum | TBD |
| ResetBillingCycle | Boolean | if `true`, it resets the subscription’s billing cycle anchor to the current time. If `false`, the quantity changes will be applied to a next invoice date. |
| Lines | Dictionary | TBD |
##### Examples
```sql
TBD
```
#### SetProductLines(Text,Text[100],Dictionary,Boolean)
![](https://img.shields.io/badge/version-Unreleased-blue)
```sql
[NonDebuggable]
SetProductLines(SecretKey:Text;ProductID:Text[100];var Lines:Dictionary;ShowConfirmation:Boolean)
```
##### Parameters
| Name | Type | Description |
| - | - | - |
| SecretKey | Text | [Stripe Secret Key](#common-parameters) |
| ProductID | Text\[100\] | [Stripe Product ID](#common-parameters) |
| Lines | Dictionary | TBD |
| ShowConfirmation | Boolean | If `true`, the confirmation dialog pops up with upcoming invoice information and information about what the next payment will look like and asks the user to proceed. |
##### Examples
```sql
TBD
```
#### SetProductLines(Text,Text[100],Enum,Dictionary,Boolean)
![](https://img.shields.io/badge/version-Unreleased-blue)
```sql
[NonDebuggable]
SetProductLines(SecretKey:Text;ProductID:Text[100];Scope:Enum;var Lines:Dictionary;ShowConfirmation:Boolean)
```
##### Parameters
| Name | Type | Description |
| - | - | - |
| SecretKey | Text | [Stripe Secret Key](#common-parameters) |
| ProductID | Text\[100\] | [Stripe Product ID](#common-parameters) |
| Scope | Enum | TBD |
| Lines | Dictionary | TBD |
| ShowConfirmation | Boolean | If `true`, the confirmation dialog pops up with upcoming invoice information and information about what the next payment will look like and asks the user to proceed. |
##### Examples
```sql
TBD
```
#### SetProductLines(Text,Text[100],Boolean,Dictionary,Boolean)
![](https://img.shields.io/badge/version-Unreleased-blue)
```sql
[NonDebuggable]
SetProductLines(SecretKey:Text;ProductID:Text[100];ResetBillingCycle:Boolean;var Lines:Dictionary;ShowConfirmation:Boolean)
```
##### Parameters
| Name | Type | Description |
| - | - | - |
| SecretKey | Text | [Stripe Secret Key](#common-parameters) |
| ProductID | Text\[100\] | [Stripe Product ID](#common-parameters) |
| ResetBillingCycle | Boolean | if `true`, it resets the subscription’s billing cycle anchor to the current time. If `false`, the quantity changes will be applied to a next invoice date. |
| Lines | Dictionary | TBD |
| ShowConfirmation | Boolean | If `true`, the confirmation dialog pops up with upcoming invoice information and information about what the next payment will look like and asks the user to proceed. |
##### Examples
```sql
TBD
```
#### SetProductLines(Text,Text[100],Enum,Boolean,Dictionary,Boolean)
![](https://img.shields.io/badge/version-Unreleased-blue)
```sql
[NonDebuggable]
SetProductLines(SecretKey:Text;ProductID:Text[100];Scope:Enum;ResetBillingCycle:Boolean;var Lines:Dictionary;ShowConfirmation:Boolean)
```
##### Parameters
| Name | Type | Description |
| - | - | - |
| SecretKey | Text | [Stripe Secret Key](#common-parameters) |
| ProductID | Text\[100\] | [Stripe Product ID](#common-parameters) |
| Scope | Enum | TBD |
| ResetBillingCycle | Boolean | if `true`, it resets the subscription’s billing cycle anchor to the current time. If `false`, the quantity changes will be applied to a next invoice date. |
| Lines | Dictionary | TBD |
| ShowConfirmation | Boolean | If `true`, the confirmation dialog pops up with upcoming invoice information and information about what the next payment will look like and asks the user to proceed. |
##### Examples
```sql
TBD
```
### ShowNotification (Method)
Force Subscription Management to show a notification to the user to act on the subscription. It will return false if there is nothing to show.
#### ShowNotification(Text,Text[100])
<a href="../References/Changelog.md#1040-2020-12-13" style="border-bottom-width:0px;background:transparent;padding:0px;">![](https://img.shields.io/badge/version-v1.0.4.0-blue)</a>
```sql
[NonDebuggable]
ShowNotification(SecretKey:Text;ProductID:Text[100]):Boolean
```
##### Parameters
| Name | Type | Description |
| - | - | - |
| SecretKey | Text | [Stripe Secret Key](#common-parameters) |
| ProductID | Text\[100\] | [Stripe Product ID](#common-parameters) |
##### Returns
| Type | Description |
| - | - |
| Boolean | Indicates if any notification shows |
##### Examples
```sql
pageextension 50000 MyExtension extends "Customer Card"
{
    trigger OnOpenPage()
    begin
        CurrPage.Editable := IsActive()
    end;

    [NonDebuggable]
    procedure IsActive(): Boolean
    var
        SecretProvider: Codeunit "App Key Vault Secret Provider";
        SubscriptionMgt: Codeunit SubscriptionMgt_SM_TSL;
        SecretKey: Text;
        ProductID: Text[100];
    begin
        if SecretProvider.TryInitializeFromCurrentApp() then
            if SecretProvider.GetSecret('SecretKey', SecretKey) and
               SecretProvider.GetSecret('MyAppProductID', ProductID)
            then
                if SubscriptionMgt.IsActive(SecretKey, ProductID) then
                  exit(true)
                else
                  SubscriptionMgt.ShowNotification(SecretKey, ProductID);
    end;
}
```
## Mocking API
### RequestMocking (Method)
Request service mocking by key provided.
#### RequestMocking(Text)
<a href="../References/Changelog.md#1040-2020-12-13" style="border-bottom-width:0px;background:transparent;padding:0px;">![](https://img.shields.io/badge/version-v1.0.4.0-blue)</a>
```sql
[NonDebuggable]
RequestMocking(PrivateKey:Text)
```
##### Parameters
| Name | Type | Description |
| - | - | - |
| PrivateKey | Text | Private key used to authorize mocking |
##### Examples
```sql
codeunit 50002 "MyExtensionTest"
{
    Subtype = Test;

    [Test]
    procedure TestMyExtension();
    var
        SecretProvider: Codeunit "App Key Vault Secret Provider";
        SubscriptionMgt: Codeunit SubscriptionMgt_SM_TSL;
        MockAuthKey: Text;
    begin
        // [Scenario] Check if customer card is editable when subscription is active
        // [Given] my extension is installed and subscription is active
        SecretProvider.TryInitializeFromCurrentApp();
        SecretProvider.GetSecret('MockAuthKey', MockAuthKey);
        SubscriptionMgt.RequestMocking(MockAuthKey);
        SubscriptionMgt.SetMock(false, true, 'Business');
        // [When] user opens a customer card 
        ...
        // [Then] customer card page is editable
        ...
    end;
}
```
### AuthorizeMocking (Method)
Allows further calls to the service to be mocked using provided private key.
#### AuthorizeMocking(Text)
<a href="../References/Changelog.md#1040-2020-12-13" style="border-bottom-width:0px;background:transparent;padding:0px;">![](https://img.shields.io/badge/version-v1.0.4.0-blue)</a>
```sql
[NonDebuggable]
AuthorizeMocking(PrivateKey:Text)
```
##### Parameters
| Name | Type | Description |
| - | - | - |
| PrivateKey | Text | Private key used to authorize mocking |
##### Examples
```sql
pageextension 50000 MyExtension extends "Customer Card"
{
    trigger OnOpenPage()
    begin
        CurrPage.Editable := IsActive()
    end;

    [NonDebuggable]
    procedure IsActive(): Boolean
    var
        SecretProvider: Codeunit "App Key Vault Secret Provider";
        SubscriptionMgt: Codeunit SubscriptionMgt_SM_TSL;
        SecretKey: Text;
        ProductID: Text[100];
        MockAuthKey: Text;
    begin
        if SecretProvider.TryInitializeFromCurrentApp() then
            if SecretProvider.GetSecret('SecretKey', SecretKey) and
               SecretProvider.GetSecret('MyAppProductID', ProductID) and
               SecretProvider.GetSecret('MockAuthKey', MockAuthKey)
            then begin
                SubscriptionMgt.AuthorizeMocking(MockAuthKey);
                exit(SubscriptionMgt.IsActive(SecretKey, ProductID));
            end
    end;
}
```
### SetMock (Method)
Sets response which `SubscriptionMgt_SM_TSL` will reply during test execution.
#### SetMock(Boolean,Boolean,Text)
![](https://img.shields.io/badge/version-v1.0.0.0-blue)
```sql
[NonDebuggable]
SetMock(IsTrialing:Boolean;IsActive:Boolean;PriceName:Text)
```
##### Parameters
| Name | Type | Description |
| - | - | - |
| IsTrialing | Boolean | Defines response for `IsTrialing` method |
| IsActive | Boolean | Defines response for `IsActive` method |
| PriceName | Text | Defines response for `GetPriceName` method |
##### Examples
```sql
codeunit 50002 "MyExtensionTest"
{
    Subtype = Test;

    [Test]
    procedure TestMyExtension();
    var
        SecretProvider: Codeunit "App Key Vault Secret Provider";
        SubscriptionMgt: Codeunit SubscriptionMgt_SM_TSL;
        MockAuthKey: Text;
    begin
        // [Scenario] Check if customer card is editable when subscription is active
        // [Given] my extension is installed and subscription is active
        SecretProvider.TryInitializeFromCurrentApp();
        SecretProvider.GetSecret('MockAuthKey', MockAuthKey);
        SubscriptionMgt.RequestMocking(MockAuthKey);
        SubscriptionMgt.SetMock(false, true, 'Business');
        // [When] user opens a customer card 
        ...
        // [Then] customer card page is editable
        ...
    end;
}
```

## See Also
- [Stripe Schema](StripeSchema.md)
- [Stripe Lifecycle](StripeLifecycle.md)
- [Security & Privacy](../Overview/Security&Privacy.md)