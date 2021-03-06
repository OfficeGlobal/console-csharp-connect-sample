---
page_type: sample
products:
- office-outlook
- ms-graph
languages:
- csharp
description: "В этом примере показано, как подключить консольное приложение Windows к рабочей или учебной школе Microsoft (Azure Active Directory) или личной (Microsoft) учетной записи с помощью API-интерфейса Microsoft Graph для отправки электронной почты."
extensions:
  contentType: samples
  technologies:
  - Microsoft Graph 
  - Microsoft identity platform
  services:
  - Microsoft identity platform
  - Outlook
  createdDate: 12/11/2017 1:55:47 PM
---
# Пример подключения C# консоли Microsoft Graph

## Содержание

* [Введение](#introduction)
* [Предварительные требования](#prerequisites)
* [Клонирование или скачивание репозитория](#cloning-or-downloading-repo)
* [Настройка клиента Azure AD](#configuring-Azure-AD-tenant )
* [Настройка примера для использования клиента Azure AD](#configuring-sample-to-use-Azure-AD-tenant)
* [Сборка и запуск примера](#build-and-run-sample)
* [Вопросы и комментарии](#questions-and-comments)
* [Участие](#contributing)
* [Дополнительные ресурсы](#additional-resources)

## Введение

В этом примере показано, как подключить консольное приложение Windows к рабочей или учебной (Azure Active Directory) либо личной учетной записи Майкрософт с помощью API Microsoft Graph для отправки электронной почты. Он использует Microsoft Graph API для получения изображения профиля пользователя, загрузки изображения в OneDrive, создания ссылки для обмена и отправки электронного письма, содержащего фотографию в виде вложения и ссылку для обмена в ее тексте. Для работы с данными, возвращаемыми Microsoft Graph, используется клиентская библиотека Microsoft Graph .NET. В этом примере используется конечная точка Azure AD v2.0, которая позволяет пользователям входить в систему с использованием своих личных или рабочих или школьных учетных записей Microsoft.

Для проверки подлинности в этом примере используется библиотека Microsoft Authentication Library (MSAL).

## Предварительные требования

Для этого примера требуются приведенные ниже компоненты.

- [Visual Studio](https://www.visualstudio.com/en-us/downloads) в C# версии 7 и более поздних. 
-  Учетная запись [Microsoft](www.outlook.com) или [Office 365 для бизнеса](https://msdn.microsoft.com/en-us/office/office365/howto/setup-development-environment#bk_Office365Account).
- Клиент Azure Active Directory (Azure AD). Дополнительную информацию о том, как получить клиента Azure AD, см. В разделе [Как получить клиента Azure AD](https://azure.microsoft.com/en-us/documentation/articles/active-directory-howto-tenant/).

## Как запустить этот образец

<a name="cloning-or-downloading-repo"></a>
### Шаг 1. Клонировать или скачать этот репозиторий

Из вашей оболочки или командной строки:

`git clone https://github.com/microsoftgraph/console-csharp-connect-sample`

<a name="configuring-Azure-AD-tenant"></a>
### Шаг 2. Настройка клиента Azure AD 

1. Войдите на [портал Azure](https://portal.azure.com) с помощью личной учетной записи Майкрософт либо рабочей или учебной учетной записи.
2. Если ваша учетная запись предоставляет вам доступ более чем к одному арендатору, выберите свою учетную запись в верхнем правом углу и задайте для сеанса портала нужный клиент Azure AD (с помощью **Switch Directory**).
3. В левой области навигации выберите службу **Azure Active Directory**, а затем выберите **Регистрация приложений (предварительный просмотр)**.

#### Зарегистрировать клиентское приложение
![](https://github.com/nicolesigei/console-csharp-connect-sample/blob/master/readme-images/registrations.png)
1. На странице **регистрации приложений** выберите **зарегистрировать приложение**.
1. На странице **регистрацию приложений (Предварительная версия)** выберите **зарегистрировать приложение**.
2. При появлении страницы **зарегистрировать приложение** введите сведения о регистрации в приложении: в разделе **имя** введите понятное имя приложения, которое будет отображаться для пользователей приложения, например 
- консольное приложение для Microsoft Graph`.
- в разделе **Поддерживаемые типы учетных записей** выберите **учетные записи в любом организационном каталоге и личных учетных записях Майкрософт (например, Skype, Xbox Outlook.com)**.
— Чтобы создать приложение, нажмите кнопку ** зарегистрировать**.
3. На странице **Обзор** приложения найдите значение **Идентификатор приложения (клиент)** и запишите его, чтобы использовать его позже. Для этого проекта вам потребуется настроить файл конфигурации Visual Studio.
4. В списке страниц приложения выберите **проверку подлинности**

    - Используйте * urn: ietf: wg: oauth: 2.0: oob * в текстовом поле ** Redirect URI ** и выберите ** Type ** в качестве публичного клиента (для мобильных и настольных компьютеров)
![](https://github.com/nicolesigei/console-csharp-connect-sample/blob/master/readme-images/redirect.png)
- В *предложенных URI перенаправления для общедоступных клиентов (мобильных, настольных)* установите второй флажок, чтобы приложение могло работать с библиотеками MSAL, используемыми в приложении. (Поле должно содержать параметр *urn: ietf: wg: oauth: 2.0: oob*).
5. В списке страниц приложения выберите **разрешения API**
— щелкните **добавить разрешение**, а затем
— убедитесь, что выбрана вкладка **Microsoft API**.
— В *часто используемые API Microsoft API*, щелкните в **Microsoft Graph**.
— В разделе **делегированные разрешения** убедитесь, что установлены нужные разрешения. **User.Read**, **Mail.Send** и **файлов. ReadWrite**. При необходимости используйте поле "Поиск".
— Нажмите кнопку **добавить разрешения**.

<a name="configuring-sample-to-use-Azure-AD-tenant"></a>
### Этап 3. Настройка примера для использования клиента Azure AD

В приведенных ниже инструкциях *идентификатор клиента* совпадает с *Идентификатором приложения * или *идентификатор приложения*.

Откройте решение в Visual Studio для настройки проектов.

#### Настроить клиентский проект

1. В папке *console-csharp-connect-sample* переименуйте файл `appsettings.json.example` в `appsettings.json`
1. Откройте и отредактируйте файл `appsettings.json`, чтобы внести следующие изменения
    1. Найдите строку, где `ClientId` установлен как `YOUR_CLIENT_ID_HERE`, и замените существующее значение идентификатором приложения (клиента) приложения `Console App for Microsoft Graph`, скопированным с портала Azure.

<a name="build-and-run-sample"></a>
### Этап 4. Сборка и запуск примера 

1. Откройте пример решения в Visual Studio.
2. Нажмите клавишу F5 для сборки и запуска примера. Это восстановит зависимости пакета NuGet и откроет консольное приложение.
3. При появлении запроса войдите в свою учетную запись Microsoft и согласитесь с разрешениями, которые необходимы приложению.
4. Следуйте инструкциям, чтобы отправить сообщение из своей учетной записи себе или кому-либо еще.
   
## Вопросы и комментарии

Мы будем рады получить ваши отзывы об API консольного приложения Microsoft Graph. Вы можете отправлять нам свои вопросы и предложения с помощью вкладки [Проблемы](https://github.com/microsoftgraph/console-csharp-connect-sample/issues) этого репозитория.

Общие вопросы о разработке решений для Microsoft Graph следует задавать на сайте [Stack Overflow](https://stackoverflow.com/questions/tagged/microsoftgraph). Обязательно помечайте свои вопросы и комментарии тегом \[microsoftgraph].

## Участие ##

Если вы хотите добавить код в этот пример, просмотрите статью [CONTRIBUTING.MD](/CONTRIBUTING.md).

Этот проект соответствует [Правилам поведения разработчиков открытого кода Майкрософт](https://opensource.microsoft.com/codeofconduct/). Дополнительные сведения см. в разделе [вопросов и ответов о правилах поведения](https://opensource.microsoft.com/codeofconduct/faq/). Если у вас возникли вопросы или замечания, напишите нам по адресу [opencode@microsoft.com](mailto:opencode@microsoft.com).
  
## Дополнительные ресурсы

- [Другие примеры приложений, подключающихся с использованием Microsoft Graph](https://github.com/MicrosoftGraph?utf8=%E2%9C%93&query=-Connect)
- [Microsoft Graph](https://developer.microsoft.com/en-us/graph)

## Авторские права
(c) Корпорация Майкрософт (Microsoft Corporation), 2019. Все права защищены.
