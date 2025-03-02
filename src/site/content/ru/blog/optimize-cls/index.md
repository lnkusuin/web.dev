---
title: Оптимизация совокупного смещения макета
subhead: Узнайте, как избежать внезапных смещений макета, чтобы улучшить взаимодействие с пользователем
authors:
  - addyosmani
date: 2020-05-05
updated: 2021-08-17
hero: image/admin/74TRx6aETydsBGa2IZ7R.png
description: Совокупное смещение макета (CLS)это показатель, который количественно определяет, как часто пользователи сталкиваются с внезапными смещения содержимого страницы. В этой статье мы рассмотрим, как оптимизировать распространенные причины плохих значений CLS, такие как изображения и плавающие фреймы без размеров, или динамический контент.
alt: Смещение макета может внезапно подтолкнуть контент, который вы читаете или собираетесь щелкнуть дальше по странице, что приведет к ухудшению взаимодействия с пользователем. Резервирование места для динамического контента, вызывающего смещения макета, приводит к более приятному опыту использования страницы.
tags:
  - blog
  - performance
  - web-vitals
---

{% YouTube id = 'AQqFZ5t8uNc', startTime = '88 '%}

«Я собирался щелкнуть по нему! Почему он сдвинулся? 😭»

Смещение макета может отвлекать пользователей. Представьте, что вы начали читать статью, как вдруг элементы смещаются по странице, сбивая вас с толку и заставляя снова искать строчку, на который вы остановились. Такое очень часто встречается в Интернете, в том числе при чтении новостей или попытке нажать кнопки «Поиск» или «Добавить в корзину». Такие ситуации с визуальным смещением контента раздражают и расстраивают. Они часто возникают, когда видимые элементы смещаются из-за того, что другой элемент был внезапно добавлен на страницу или изменен в размерах.

[CLS (Совокупное смещение макета)](/cls)[метрика Core Web Vitals](/vitals), которая измеряет нестабильность контента путем суммирования оценок смещения для каждого смещения макета (за исключением периода в 500 миллисекунд после ввода пользователя). Метрика оценивает, насколько видимый контент сместился в области просмотра, а также учитывает расстояние, на которое сдвинулись затронутые элементы.

В этой статье мы рассмотрим, как оптимизировать распространенные причины смещений макета.

<picture>
  <source srcset="{{ "image/tcFciHGuF3MxnTr1y5ue01OGLBn2/9mWVASbWDLzdBUpVcjE1.svg" | imgix }}" media="(min-width: 640px)">
  {% Img src="image/tcFciHGuF3MxnTr1y5ue01OGLBn2/uqclEgIlTHhwIgNTXN3Y.svg", alt="Хорошие значения CLS ниже 0,1, плохие значения больше 0,25 и все промежуточные значения требуют улучшения", width="384", height="96" %}
</picture>

Наиболее частые причины плохого показателя CLS:

- изображения без размеров;
- рекламные объявления, встраиваемые объекты и плавающие фреймы без размеров;
- динамически добавляемый контент;
- веб-шрифты, вызывающие побочные эффекты FOIT или FOUT;
- действия, ожидающие ответа сети перед обновлением DOM.

## Изображения без размеров 🌆

**Резюме:** всегда включайте атрибуты `width` и `height` для изображений и видеоэлементов. В качестве альтернативы зарезервируйте необходимое пространство с помощью [CSS-свойства aspect-ratio](https://css-tricks.com/aspect-ratio-boxes/). Такой подход гарантирует, что браузер сможет выделить правильный объем места в документе во время загрузки изображения.

  <figure>{% Video src=["video/tcFciHGuF3MxnTr1y5ue01OGLBn2/10TEOBGBqZm1SEXE7KiC.webm", "video/tcFciHGuF3MxnTr1y5ue01OGLBn2/WOQn6K6OQcoElRw0NCkZ.mp4"], poster="image/tcFciHGuF3MxnTr1y5ue01OGLBn2/8wKRITUkK3Zrp5jvQ1Xw.jpg", controls=true, loop=true, muted=true %} <figcaption> Изображения без указания высоты и ширины. </figcaption></figure>

  <figure>{% Video src=["video/tcFciHGuF3MxnTr1y5ue01OGLBn2/38UiHViz44OWqlKFe1VC.webm", "video/tcFciHGuF3MxnTr1y5ue01OGLBn2/sFxDb36aEMvTPIyZHz1O.mp4"], poster="image/tcFciHGuF3MxnTr1y5ue01OGLBn2/wm4VqJtKvove6qjiIjic.jpg", controls=true, loop=true, muted=true %} <figcaption> Изображения с указанной шириной и высотой. </figcaption></figure>

<figure>{% Img src="image/tcFciHGuF3MxnTr1y5ue01OGLBn2/A2OyrzSXuW1qYGWAarGx.png", alt="Отчет Lighthouse, показывающий значения CLS до и после установки размеров для изображений", width="800", height="148" %} <figcaption> Lighthouse 6.0, влияние установки размеров для изображений на CLS. </figcaption></figure>

### Предыстория

На заре Интернета разработчики добавляли атрибуты `width` и `height` к своим тегам `<img>`, чтобы обеспечить достаточное пространство на странице до того, как браузер начнет загружать изображения. Это позволяло свести к минимуму пересчет положения элементов на странице и их перекомпоновку.

```html
<img src="puppy.jpg" width="640" height="360" alt="Puppy with balloons" />
```

Вы можете заметить, что в примере выше атрибуты `width` и `height` не содержат единицы измерения. «Пиксельные» размеры обеспечивают резервирование области 640x360. Чтобы вписаться в это пространство, изображение будет растягиваться, независимо от своих истинных размеров.

С появлением [адаптивного веб-дизайна](https://www.smashingmagazine.com/2011/01/guidelines-for-responsive-web-design/) разработчики начали опускать атрибуты `width` и `height`, и вместо этого для изменения размера изображений стали использовать CSS:

```css
img {
  width: 100%; /* or max-width: 100%; */
  height: auto;
}
```

Но у такого похода есть недостаток: место под изображение можно выделить только после того, как оно начнет загружаться и браузер сможет определить его размеры. Страница будет перерисовываться по мере загрузки и появления каждого изображения на экране. Часто случалось, что текст появлялся на экране внезапно. Это было не очень удобно для пользователя.

Вот тут и появляется соотношение сторон. Соотношение сторон изображенияэто отношение его ширины к его высоте. Обычно оно выражается в виде двух чисел, разделенных двоеточием (например, 16:9 или 4:3). При соотношении сторон x:y изображение имеет ширину «x» единиц и высоту «y» единиц.

Это означает, что если мы знаем один из линейных размеров, то другой можно определить. Для соотношения сторон 16:9:

- если puppy.jpg имеет высоту 360 пикселей, то ширина изображения составит 360 x (16/9) = 640 пикселей;
- если puppy.jpg имеет ширину 640 пикселей, то высота изображения составит 640 x (9/16) = 360 пикселей.

Знание соотношения сторон позволяет браузеру рассчитать и зарезервировать достаточное пространство для высоты и соответствующей ширины изображения.

### Современный способ

Современные браузеры устанавливают соотношение сторон изображений по умолчанию на основе атрибутов ширины и высоты изображения, поэтому полезно задать их для предотвращения смещений макета. Благодаря CSS Working Group разработчики могут задать атрибуты `width` и `height` обычным способом:

```html
<!-- set a 640:360 i.e a 16:9 - aspect ratio -->
<img src="puppy.jpg" width="640" height="360" alt="Puppy with balloons" />
```

… А таблицы стилей [UA stylesheets](https://developer.mozilla.org/docs/Web/CSS/Cascade#User-agent_stylesheets) всех браузеров добавляют [соотношение сторон по умолчанию](https://html.spec.whatwg.org/multipage/rendering.html#attributes-for-embedded-content-and-images) на основании существующих атрибутов `width` и `height`:

```css
img {
  aspect-ratio: attr(width) / attr(height);
}
```

Здесь соотношение сторон вычисляется на основании атрибутов `width` и `height` до загрузки изображения. Эта информация предоставляется в самом начале расчета макета. Как только изображению задается определенная ширина (например, `width: 100%`), его высота вычисляется с помощью соотношения сторон.

Совет. Если вам трудно представить соотношение сторон, воспользуйтесь удобным [калькулятором.](https://aspectratiocalculator.com/16-9.html)

Вышеупомянутые изменения соотношения сторон изображения были реализованы в [Firefox](https://bugzilla.mozilla.org/show_bug.cgi?id=1547231) и [Chromium](https://bugs.chromium.org/p/chromium/issues/detail?id=979891) и будут добавлены в [WebKit](https://twitter.com/smfr/status/1220051332767174656) (Safari).

Чтобы фантастически глубоко погрузиться в тему о соотношении сторон и адаптивных изображениях, прочитайте статью [«Загрузка страницы без подвисания с медиавыражением, проверяющим соотношение сторон»](https://blog.logrocket.com/jank-free-page-loading-with-media-aspect-ratios/).

Если ваше изображение находится в контейнере, вы можете использовать CSS, чтобы изменить размер изображения до ширины этого контейнера. Устанавливаем `height: auto;`, чтобы высота изображения не была фиксированной (например, `360px`).

```css
img {
  height: auto;
  width: 100%;
}
```

**А как насчет адаптивных изображений?**

При работе с [адаптивными изображениями](/serve-responsive-images) атрибут `srcset` определяет изображения, среди которых браузер будет выбирать подходящее, и размер каждого изображения. Чтобы обеспечить возможность установки атрибутов ширины и высоты `<img>`, каждое изображение должно использовать одинаковое соотношение сторон.

```html
<img
  width="1000"
  height="1000"
  src="puppy-1000.jpg"
  srcset="puppy-1000.jpg 1000w, puppy-2000.jpg 2000w, puppy-3000.jpg 3000w"
  alt="Puppy with balloons"
/>
```

А как насчет метода [художественного направления](https://developer.mozilla.org/docs/Learn/HTML/Multimedia_and_embedding/Responsive_images#Art_direction) ?

Основная идея метода художественного направленияотображать разные изображения в зависимости от размеров экрана устройства, например, обрезанное изображение в узких окнах просмотра и полное изображение на настольных компьютерах.

```html
<picture>
  <source media="(max-width: 799px)" srcset="puppy-480w-cropped.jpg" />
  <source media="(min-width: 800px)" srcset="puppy-800w.jpg" />
  <img src="puppy-800w.jpg" alt="Puppy with balloons" />
</picture>
```

Вероятно, что эти изображения могут иметь разное соотношение сторон, и браузеры всё еще оценивают, каким должно быть наиболее эффективное решение, в том числе следует ли указывать размеры во всех источниках. Пока общее решение не принято, перекомпоновка в этом случае всё ещё возможна.

## Рекламные объявления, встраиваемые объекты и плавающие фреймы без размеров 📢😱

### Рекламные объявления

Рекламаодин из основных факторов, способствующих изменению макета в Интернете. Рекламные сети и веб-мастеры часто поддерживают динамические размеры объявлений. Размеры объявлений увеличивают эффективность/доход за счет более высоких показателей кликов и большего числа объявлений, участвующих в аукционе. К сожалению, это может привести к неоптимальному взаимодействию с пользователем из-за того, что реклама выталкивает видимый контент, который вы просматриваете, вниз по странице.

В течение жизненного цикла объявления многие моменты могут вызвать смещение макета:

- когда сайт вставляет рекламный контейнер в DOM;
- когда сайт изменяет размер рекламного контейнера с помощью собственного кода;
- когда библиотека рекламных тегов загружается (и меняет размер рекламного контейнера);
- когда объявление заполняет контейнер (и меняет размер, если окончательное объявление имеет другой размер).

Хорошая новостьсайты могут применять передовые методы, чтобы уменьшить сдвиг. Смещение в макете можно уменьшить за счет следующих действий:

- Статически зарезервировать место для рекламного блока.
    - Другими словами, задайте стиль для элемента до загрузки библиотеки тегов объявлений.
    - При размещении рекламы в потоке контента убедитесь, что сдвиги устранены, зарезервировав место под размер рекламного блока. Эти объявления *не должны* вызывать смещения макета, если загружаются за пределами области просмотра.
- Будьте осторожны при размещении нелипких баннеров в верхней части области просмотра.
    - В приведенном ниже примере рекомендуется переместить рекламу под логотип world vision и зарезервировать достаточное место для этого блока.
- Если под рекламный блок на странице зарезервировано пространство, но само объявление не появилось, не стоит сворачивать это пространство, лучше показать там заполнитель, чтобы не сдвигать контент.
- Устраните смещения, зарезервировав максимально возможный размер для рекламного блока.
    - Это работает, но может остаться пустое место, если блок заполнит реклама меньшего размера.
- Выберите наиболее вероятный размер рекламного блока на основании исторических данных.

На некоторых сайтах уменьшить смещение макета можно за счет первоначального сворачивания места под рекламный блок, если вероятность его заполнения невелика. Не существует простого способа каждый раз выбирать точный размер, если только вы сами не контролируете показ рекламы.

  <figure>{% Video src=["video/tcFciHGuF3MxnTr1y5ue01OGLBn2/bmxqj3kZyplh0ncMAt7x.webm", "video/tcFciHGuF3MxnTr1y5ue01OGLBn2/60c4T7aYOsKtZlaWBndS.mp4"], poster="image/tcFciHGuF3MxnTr1y5ue01OGLBn2/rW77UoJQBHHehihkw2Rd.jpg", controls=true, loop=true, muted=true %} <figcaption> Объявления без достаточного зарезервированного места. </figcaption></figure>

  <figure>{% Video src=["video/tcFciHGuF3MxnTr1y5ue01OGLBn2/tyUFKrue5vI9o5qKjP42.webm", "video/tcFciHGuF3MxnTr1y5ue01OGLBn2/hVxty51kdN1w5BuUvj2O.mp4"], poster="image/tcFciHGuF3MxnTr1y5ue01OGLBn2/rW77UoJQBHHehihkw2Rd.jpg", controls=true, loop=true, muted=true %} <figcaption> Объявления с достаточным зарезервированным местом. </figcaption></figure>

<figure>{% Img src="image/tcFciHGuF3MxnTr1y5ue01OGLBn2/cX6R4ACb4uVKlUb0cv1c.png", alt="Отчет Lighthouse, показывающий значения CLS до и после резервирования места для рекламных баннеров", width="800", height="148" %} <figcaption> Lighthouse 6.0, влияние резервирования места для рекламного баннера на CLS </figcaption></figure>

#### Статическое резервирование места для рекламного блока

Статически задавайте стиль для DOM-элементов блока с теми же размерами, которые передаются в вашу библиотеку тегов. Это поможет избежать смещения макета при загрузке библиотеки. Если этого не сделать, библиотека может изменить размер элемента блока после верстки страницы.

Также учитывайте размеры объявлений меньшего размера. Если подается объявление меньшего размера, веб-мастер может сверстать (больший) контейнер, чтобы избежать смещения макета. У такого подхода есть недостатокувеличивается количество пустого пространства, поэтому попытайтесь найти компромиссное решение.

#### Избегайте размещения рекламы в верхней части области просмотра

Объявления в верхней части области просмотра могут вызывать большее смещение макета, чем объявления в середине. Это связано с тем, что под объявлениями в верхней части располагается больше контента, а значит, больше элементов перемещается, когда объявление вызывает сдвиг макета. И наоборот, объявления, расположенные в середине области просмотра, могут смещать не так много элементов, так как контент над ними с меньшей вероятностью будет смещаться.

### Встраиваемые объекты и плавающие фреймы

Встраиваемые виджеты позволяют встраивать переносимый веб-контент на вашу страницу (например, видео с YouTube, карты из Google Карты, публикации в социальных сетях и т. д.). Такие встраиваемые объекты могут иметь несколько форм:

- резервный HTML-код и тег JavaScript, преобразующий резервный вариант в элегантный встраиваемый объект;
- встроенный фрагмент кода HTML;
- встроенный плавающий фрейм.

В случае встраиваемых объектов размер встраиваемого контента, как правило, не известен заранее (например, публикация в социальных сетяхбудет ли там встроенное изображение, видео или текст в несколько строк). В результате платформы, предлагающие встраивание, не всегда резервируют достаточно места для встраиваемых объектов, что может привести к смещению макета при их окончательной загрузке.

  <figure>{% Video src=["video/tcFciHGuF3MxnTr1y5ue01OGLBn2/NRhY88MbNJxe4o0F52eS.webm", "video/tcFciHGuF3MxnTr1y5ue01OGLBn2/PzOpQnPH88Ymbe3MCH7B.mp4"], poster="image/tcFciHGuF3MxnTr1y5ue01OGLBn2/w0TM1JilKPQktQgb94un.jpg", controls=true, loop=true, muted=true %} <figcaption> Встраиваемые объекты без зарезервированного места. </figcaption></figure>

  <figure>{% Video src=["video/tcFciHGuF3MxnTr1y5ue01OGLBn2/aA8IoNeQTCEudE45hYzh.webm", "video/tcFciHGuF3MxnTr1y5ue01OGLBn2/xjCWjSv4Z3YB29jSDGae.mp4"], poster="image/tcFciHGuF3MxnTr1y5ue01OGLBn2/gtYqKkoEse47ErJPqVjg.jpg", controls=true, loop=true, muted=true %} <figcaption> Встраиваемые объекты с зарезервированным местом. </figcaption></figure>

<figure>{% Img src="image/tcFciHGuF3MxnTr1y5ue01OGLBn2/2XaMbZBmUit1Vz8UBshH.png", alt="Отчет Lighthouse, показывающий значения CLS до и после резервирования места для встраиваемого объекта", width="800", height="148" %} <figcaption> Lighthouse 6.0, влияние резервирования места для встраиваемого объекта на CLS </figcaption></figure>

Чтобы обойти эти проблемы, вы можете свести к минимуму CLS, предварительно вычислив размер достаточного места для встраиваемого объекта с заполнителем или резервным вариантом. Вот один из рабочих процессов, который вы можете использовать для встраиваемых объектов:

- Определите окончательную высоту встраиваемого объекта, проверив его с помощью инструментов разработчика в браузере.
- После загрузки встраиваемого объекта размер плавающего фрейма изменится, и соответственно изменится его содержимое.

Учитывайте размеры, и предусмотрите заполнитель на месте встраиваемого объекта. Возможно, потребуется учесть тонкие различия в размерах объявлений/заполнителей для разных форм-факторов с помощью медиа-запросов.

### Динамический контент 📐

**Резюме:** Избегайте вставлять контент поверх существующего, кроме случаев, когда это требуется в ответ на взаимодействие с пользователем. Это гарантирует, что любые изменения макета будут ожидаемыми.

Вы наверняка сталкивались со смещениями макета из-за пользовательского интерфейса, который появлялся вверху или внизу области просмотра, когда вы пытались загрузить сайт. Как и в случае с рекламой, это часто происходит из-за баннеров и форм, смещающих остальное содержимое страницы:

- «Подпишитесь на наши новости!» (Эй, сбавьте обороты! Мы же только что встретились!)

- «Связанный контент».

- «Установите наше приложение [iOS/Android]».

- «Мы всё ещё принимаем заказы».

- «Уведомление о GDPR».

    <figure>{% Video src=["video/tcFciHGuF3MxnTr1y5ue01OGLBn2/LEicZ7zHqGFrXl67Olve.webm", "video/tcFciHGuF3MxnTr1y5ue01OGLBn2/XFvOHc2OB8vUD9GbpL2w.mp4"], poster="image/tcFciHGuF3MxnTr1y5ue01OGLBn2/PF9ulVHDQOvoWendb6ea.jpg", controls=true, loop=true, muted=true %} <figcaption> Динамический контент без зарезервированного места. </figcaption></figure>

Если вам нужно отобразить эти типы возможностей пользовательского интерфейса, заранее зарезервируйте для этого достаточное пространство в области просмотра (например, с помощью заполнителя или скелета пользовательского интерфейса), чтобы при загрузке они не вызвали неожиданного смещения содержимого страницы.

В некоторых случаях динамическое добавление контента является важной частью взаимодействия с пользователем. Например, при загрузке дополнительных продуктов в список элементов или при обновлении содержимого прямой трансляции. В таких случаях есть несколько способов избежать неожиданного смещения макета:

- Замените старый контент новым контентом в контейнере фиксированного размера или используйте карусель и удалите старый контент после перехода. Не забудьте отключить любые ссылки и элементы управления до завершения перехода, чтобы предотвратить случайные щелчки или касания при поступлении нового контента.
- Попросите пользователя инициировать загрузку нового контента, чтобы он не был удивлен сдвигом (например, с помощью кнопки «Загрузить больше» или «Обновить»). Рекомендуется предварительно загрузить контент до взаимодействия с пользователем, чтобы он отображался немедленно. Напоминаем, что смещения макета, которые происходят в течение 500 мс после пользовательского ввода, не учитываются в CLS.
- Незаметно для пользователя загружайте контент за пределами области просмотра, а затем выводите пользователю уведомление о доступности контента (например, с помощью кнопки «Прокрутить вверх»).

<figure>{% Img src="image/OcYv93SYnIg1kfTihK6xqRDebvB2/TjsYVkcDf03ZOVCcsizv.png", alt="Примеры динамической загрузки контента без неожиданного смещения макета из сайта Twitter и сайта Chloé", width="800", height="458" %} <figcaption> Примеры загрузки динамического контента без непредвиденных смещений макета. Слева: загрузка содержимого прямой трансляции в Twitter. Справа: пример «Загрузить больше» на сайте Chloé. Узнайте, как команда YNAP <a href="https://medium.com/ynap-tech/how-to-optimize-for-cls-when-having-to-load-more-content-3f60f0cf561c">оптимизировала CLS при загрузке большего количества контента</a>.</figcaption></figure>

### Веб-шрифты, вызывающие побочные эффекты FOIT или FOUT

Загрузка и рендеринг веб-шрифтов может вызвать смещение макета двумя способами:

- резервный шрифт заменяется новым шрифтом (FOUTмигание нестилизованного текста);
- «невидимый» текст отображается до тех пор, пока не будет отображен новый шрифт (FOITмигание невидимого текста).

Эти побочные эффекты могут помочь минимизировать следующие инструменты:

- свойство <code>[font-display](/font-display/)</code> позволяет изменять поведение визуализации пользовательских шрифтов, оно имеет пять значений <code>auto</code>, <code>swap</code>, <code>block</code>, <code>fallback</code> и <code>optional</code>. К сожалению, все эти значения (кроме [optional](http://crrev.com/749080)) могут вызвать перекомпоновку одним из вышеуказанных способов.
- [Font Loading API](https://developers.google.com/web/fundamentals/performance/optimizing-content-efficiency/webfont-optimization#the_font_loading_api) (API загрузки шрифтов) может сократить время, необходимое для получения необходимых шрифтов.

Что касается Chrome 83, я также могу порекомендовать следующее:

- использовать тег `<link rel=preload>` для загрузки основных веб-шрифтов: у предварительно загруженного шрифта будет больше шансов попасть в первую отрисовку контента, и в этом случае смещения макета не происходит;
- объединить `<link rel=preload>` и `font-display: optional`.

Более подробную информацию см. в статье [«Предотвращение смещения макета и побочных эффектов FOIT (Мигание невидимого текста) путем предварительной загрузки дополнительных шрифтов»](/preload-optional-fonts/).

### Анимации 🏃‍♀️

**Резюме:** Используйте свойство `transform` вместо анимации свойств, которая запускает смещение макета.

При изменении значений свойств CSS может потребоваться реакция браузера на эти изменения. Ряд значений запускает повторную компоновку, отрисовку и организацию слоев, например, `box-shadow` и `box-sizing`. Некоторые свойства CSS можно изменить менее ресурсоёмким способом.

Дополнительные сведения о том, какие свойства CSS запускают смещение макета, см. в таблице [«Триггеры CSS»](https://csstriggers.com/) и статье [«Высокопроизводительная анимация»](https://www.html5rocks.com/en/tutorials/speed/high-performance-animations/).

### Инструменты разработчика 🔧

Я рад сообщить, что существует ряд инструментов для измерения и отладки CLS (Совокупного смещения макета)

[Lighthouse](https://developers.google.com/web/tools/lighthouse) [6.0](https://github.com/GoogleChrome/lighthouse/releases) и более поздние версии включают поддержку измерения CLS в лабораторных условиях. В этом релизе также будут выделены узлы, вызывающие наибольшее смещение макета.

{% Img src="image/tcFciHGuF3MxnTr1y5ue01OGLBn2/J11KOGFVAOjRMdihwX5t.jpg", alt="Lighthouse 6.0 включает поддержку измерения CLS в разделе метрик", width="800", height="309" %}

Панель [Performance](https://developers.google.com/web/tools/chrome-devtools/evaluate-performance) в DevTools показывает смещения макета в разделе **Experience** в Chrome 84. Представление **Summary** для `Layout Shift` включает совокупную оценку сдвига макета, а также прямоугольное наложение, показывающее затронутые области.

<figure>{% Img src="image/admin/ApDKifKCRNGWI2SXSR1g.jpg", alt="Записи Layout Shift отображаются на панели производительности Chrome DevTools при раскрытии раздела Experience", width="800", height="438" %}<figcaption> После записи новой трассировки на панели Performance в разделе <b>Experience</b> появляется красная полоса, отображающая запись <code>Layout Shift</code> Щелкнув по этой записи, вы сможете просмотреть затронутые элементы (например, увидеть смещенные записи из/в).</figcaption></figure>

Измерить реальные значений CLS, агрегированные на уровне источника, также можно с помощью отчета [Chrome User Experience Report](/chrome-ux-report-bigquery/). Данные CrUX CLS доступны через BigQuery, а также доступен [образец запроса](https://github.com/GoogleChrome/CrUX/blob/master/sql/cls-summary.sql) для проверки производительности CLS.

Вот и всё. Надеюсь, приведенная информация поможет сделать ваши страницы чуть менее увёртливыми :)

*Выражаем благодарность Филиппу Уолтону, Кенджи Бахе, Уоррену Мареске, Энни Салливан, Стиву Кобесу и Джильберто Кокки за их полезные обзоры.*
