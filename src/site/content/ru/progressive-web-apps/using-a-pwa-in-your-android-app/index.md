---
layout: post
title: Использование прогрессивного веб-приложения в приложении для Android
authors:
  - andreban
date: 2020-03-19
updated: 2021-12-06
description: Как открыть прогрессивное веб-приложение в приложении для Android.
tags:
  - progressive-web-apps
---

## Запуск прогрессивного веб-приложения в приложении для Android

[Прогрессивные веб-приложения](/progressive-web-apps/) (Progressive Web Apps, PWA) — это высококачественные, быстродействующие, надежные и привлекательные веб-приложения, которые выглядят и работают, как обычные приложения.

Интернет имеет невероятный охват. Пользователи могут открывать для себя новые возможности самыми разными способами, но несмотря на это они привыкли использовать приложения из магазинов операционных систем на своих устройствах. Во многих случаях пользователи уже достаточно знают о брендах или услугах, которые они ищут, и очень целенаправленны, из-за чего значения метрик вовлечения для таких пользователей превышают средний уровень.

Play Store — это магазин приложений для Android, и разработчикам часто необходимо, чтобы их прогрессивные веб-приложения можно было открывать в приложениях для Android.

Trusted Web Activity — это открытый стандарт, благодаря которому браузеры могут предоставлять полностью совместимый с веб-платформой контейнер для отображения прогрессивного веб-приложения внутри приложения для Android. Эта возможность доступна в [Chrome](https://play.google.com/store/apps/details?id=com.android.chrome) и находится в разработке для [Firefox Nightly](https://play.google.com/store/apps/details?id=org.mozilla.fenix).

### Существующие решения имеют ограниченные возможности

В приложения для Android всегда можно было добавлять веб-функции, используя определенные технологии, например [Android WebView](https://developer.android.com/reference/android/webkit/WebView), или платформы, например [Cordova](https://cordova.apache.org/).

Одно из ограничений технологии Android WebView заключается в том, что она не предназначена для замены браузера. Android WebView — это средство разработчика, позволяющее использовать пользовательский веб-интерфейс в приложениях Android, и оно не обеспечивает полный доступ к функциям современной веб-платформы, например к [средству выбора контактов](/contact-picker/), [файловой системе](/file-system-access/) и [ряду других возможностей](/fugu-status/).

Платформа Cordova была разработана, чтобы компенсировать недостатки WebView, но вследствие этого для API появились ограничения, связанные со средой Cordova. Это означает, что помимо прогрессивного веб-приложения, работающего в открытом Интернете, вам придется поддерживать дополнительную базу кода для использования API Cordova в своем приложении для Android.

Кроме того, в существующих решениях зачастую не работают должным образом функции обнаружения возможностей и могут возникать проблемы совместимости между различными версиями Android и продукцией OEM-производителей. При использовании таких решений разработчикам требуется применять дополнительные процессы обеспечения качества и нести дополнительные расходы на разработку для поиска и создания обходных путей.

### Trusted Web Activity — новый контейнер для веб-приложений в Android

Теперь разработчики могут использовать контейнеры [Trusted Web Activity](https://developers.google.com/web/updates/2019/02/using-twa) и добавлять действия запуска прогрессивных веб-приложений в свои приложения для Android. Эта технология использует браузер для отображения прогрессивных веб-приложений в полноэкранном режиме и гарантирует, что контейнеры Trusted Web Activity будут в той же степени совместимы с функциями и API веб-платформы, что и используемый браузер. Кроме того, с помощью ряда служебных программ с открытым исходным кодом можно упростить реализацию приложений для Android с использованием технологии Trusted Web Activity.

Еще одно преимущество, недоступное в других решениях, заключается в том, что для контейнера используется то же хранилище, что и для браузера. В результате можно без проблем обмениваться сведениями о состоянии входа в систему и предпочтениях пользователя между разными интерфейсами.

#### Совместимость с браузерами

Эта функция доступна в Chrome, начиная с версии 75, а в Firefox она реализована в «ночных» версиях.

### Критерии качества

Веб-разработчикам следует использовать технологию Trusted Web Activity, если нужно включить веб-контент в приложение для Android.

Веб-контент в Trusted Web Activity должен соответствовать критериям возможности установки прогрессивного веб-приложения.

Кроме того, чтобы прогрессивные веб-приложения работали как обычные приложения для Android, в [Chrome 86 внесено изменение](https://blog.chromium.org/2020/06/changes-to-quality-criteria-for-pwas.html). Согласно ему случаи, в которых не удается обработать указанные ниже сценарии, считаются сбоями.

- Не удается проверить ссылки на цифровые ресурсы при запуске приложения.
- Не удается возвратить код HTTP 200 при запросе сетевого ресурса в автономном режиме.
- В ответ на запрос на переход возвращается ошибка HTTP 404 или 5xx.

Когда в Trusted Web Activity возникает один из этих сценариев, он приводит к видимому для пользователя сбою приложения для Android. Сведения о том, как обрабатывать такие сценарии в служебных скриптах, см. в [этом руководстве](https://developer.chrome.com/docs/android/trusted-web-activity/whats-new/#updates-to-the-quality-criteria).

Кроме того, приложение должно соответствовать дополнительным критериям, связанным с ОС Android, например [требованиям политик](https://play.google.com/about/developer-content-policy/).

{% Aside 'caution' %} Если приложение предназначено в первую очередь для детей младше 13 лет, [применяются дополнительные политики Play Family](https://play.google.com/about/families/), которые могут быть несовместимы с Trusted Web Activity. {% endAside %}

<figure>{% Img src="image/tcFciHGuF3MxnTr1y5ue01OGLBn2/9Z70W3aCI8ropKpMXHcz.png", alt="Снимок экрана с оценкой Lighthouse для AirHorn, эмблемой прогрессивного веб-приложения и оценкой производительности, равной 100 баллам.", width="800", height="141" %} <figcaption> Эмблема прогрессивного веб-приложения в Lighthouse показывает, соответствует ли прогрессивное веб-приложение критериям возможности установки. </figcaption></figure>

## Инструменты

Веб-разработчикам, которые хотят использовать Trusted Web Activity, не нужно изучать новые технологии или API, чтобы преобразовывать свои прогрессивные веб-приложения в приложения для Android. В средствах Bubblewrap и PWABuilder имеются инструменты для разработчиков в виде библиотек, ИКС и программ с графическим пользовательским интерфейсом.

### Bubblewrap

Проект [Bubblewrap](https://github.com/GoogleChromeLabs/bubblewrap) позволяет создавать приложения для Android и доступен в виде библиотеки NodeJS и ИКС.

Для начальной загрузки нового проекта нужно запустить это средство и передать в него URL-адрес веб-манифеста:

```shell
npx @bubblewrap/cli init --manifest=https://pwa-directory.appspot.com/manifest.json
```

Это средство также может выполнить сборку проекта. Если запустить следующую команду, будет создано приложение для Android, которое можно отправить в Play Store:

```shell
npx @bubblewrap/cli build
```

После выполнения этой команды в корневом каталоге проекта появится файл `app-release-signed.apk`. Этот файл можно [отправить в Play Store](https://support.google.com/googleplay/android-developer/answer/3131213?hl=en-GB).

### PWABuilder

С помощью [PWABuilder](https://pwabuilder.com/) можно преобразовывать существующие веб-сайты в прогрессивные веб-приложения. Его также можно интегрировать с Bubblewrap, чтобы добавлять прогрессивные веб-приложения в приложения для Android с использованием графического пользовательского интерфейса. Разработчики PWABuilder написали [отличную публикацию в блоге](https://www.davrous.com/2020/02/07/publishing-your-pwa-in-the-play-store-in-a-couple-of-minutes-using-pwa-builder/) о том, как с помощью этого средства создавать приложения для Android.

## Проверка права собственности на прогрессивное веб-приложение в приложении для Android

Разработчик, создавший прогрессивное веб-приложение, наверняка не захочет, чтобы другой разработчик использовал его без разрешения при создании собственного приложения для Android. Чтобы этого не произошло, необходимо выполнить сопряжение приложения для Android с прогрессивным веб-приложением с помощью средства [Digital Asset Links](https://developers.google.com/digital-asset-links/v1/getting-started).

Средства Bubblewrap и PWABuilder создают необходимую конфигурацию в приложении для Android, но нужно выполнить еще одно действие — добавить файл `assetlinks.json` в прогрессивное веб-приложение.

Чтобы создать этот файл, разработчикам необходима подпись SHA-256 ключа, используемого для подписи APK-файла, который загружают пользователи.

Ключ можно сгенерировать несколькими способами. Самый простой способ узнать, каким ключом подписан APK-файл, передаваемый конечным пользователям, — загрузить этот файл из Play Store.

Чтобы не отображать неработающее приложение для пользователей, разверните приложение в [закрытом тестовом канале](https://support.google.com/googleplay/android-developer/answer/3131213?hl=en-GB), установите его на тестовое устройство и с помощью средства [Peter's Asset Link Tool](https://play.google.com/store/apps/details?id=dev.conn.assetlinkstool) создайте правильный файл `assetlinks.json` для приложения. Сделайте созданный файл `assetlinks.json` доступным по адресу `/.well-known/assetlinks.json` в проверяемом домене.

## Что делать дальше

Прогрессивное веб-приложение — это высококачественный веб-интерфейс. Trusted Web Activity — новый способ использовать такие высококачественные интерфейсы (если они соответствуют минимальным критериям качества) в приложениях для Android.

Если вы только начинаете работать с прогрессивными веб-приложениями, прочтите [наше руководство по созданию прогрессивных веб-приложений](/progressive-web-apps/). Разработчики, у которых уже есть прогрессивные веб-приложения, с помощью [Lighthouse](https://developers.google.com/web/tools/lighthouse) могут проверить, соответствуют ли их веб-приложения определенным критериям качества.

Затем с помощью средства [Bubblewrap](https://github.com/GoogleChromeLabs/bubblewrap) или [PWABuilder](https://pwabuilder.com/) создайте приложение для Android, [отправьте его в закрытый тестовый канал в Play Store](https://support.google.com/googleplay/android-developer/answer/3131213?hl=en-GB) и выполните его сопряжение с прогрессивным веб-приложением с помощью средства [Peter's Asset Link Tool](https://play.google.com/store/apps/details?id=dev.conn.assetlinkstool).

И, наконец, переместите свое приложение из закрытого тестового канала в рабочую среду.
