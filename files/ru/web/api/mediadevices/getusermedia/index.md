---
title: MediaDevices.getUserMedia()
slug: Web/API/MediaDevices/getUserMedia
translation_of: Web/API/MediaDevices/getUserMedia
---
{{APIRef("Media Capture and Streams")}}

Метод {{domxref("MediaDevices")}}**`.getUserMedia()`**, при выполнении, вызывает всплывающий диалог, запрашивающий разрешение пользователя на использование медиа устройства (камера, микрофон). Результат возвращает промис, содержащий поток, который состоит из треков (дорожек), содержащих требуемые медиа типы. Этот поток может включать, к примеру, видеотрек, созданный либо аппаратным средством, либо виртуальным видеоисточником, такими как камера, устройство видеозаписи, сервис обмена изображениями и т.д); аудиотрек, созданный физическим или виртуальным аудиоисточником, к примеру, микрофоном, аналого-цифровым преобразователем звуков и возможно иные типы треков.

Он возвращает {{jsxref("Promise")}} , который, в случае согласия пользователя, разрешается {{domxref("MediaStream")}} объектом. Если пользователь отказывает в разрешении, или медиа устройство не доступно, тогда промис отменяется с объектами типа `NotAllowedError` или `NotFoundError` соответственно.

> **Примечание:** **Примечание :** Возможно, что промис ни разрешиться, ни отмениться, в случае, когда пользователь не сделает выбор и запрос проигнорирует.

Обычно, разработчик получает доступ к единственному экземпляру объекта {{domxref("MediaDevices")}} , используя {{domxref("navigator.mediaDevices.getUserMedia()")}} метод, возвращающий поток:

```js
async function getMedia(constraints) {
  let stream = null;

  try {
    stream = await navigator.mediaDevices.getUserMedia(constraints);
    /* используем поток */
  } catch(err) {
    /* обработка ошибки */
  }
}
```

Тот же результат, но используя тип промиса :

```js
navigator.mediaDevices.getUserMedia(constraints)
.then(function(stream) {
  /* используем поток */
})
.catch(function(err) {
  /* обработка ошибки */
});
```

> **Примечание:** **Примечание :** Если документ загружен не безопасно, значение `navigator.mediaDevices` будет `undefined`, и нельзя будет использовать метод `getUserMedia()`. Смотри [Security](#security) для дополнительной информации о дальнейших вопросах безопасности, связанной с использованием метода `getUserMedia()`.

## Синтаксис

```
var promise = navigator.mediaDevices.getUserMedia(constraints);
```

### Параметры

- `constraints (ограничения)`

  - : Объект {{domxref("MediaStreamConstraints")}}, определяющий запрашиваемые медиатипы, вместе с требованиями для каждого типа.

    Тип параметра `constraints` (ограничения) - это объект типа `MediaStreamConstraints` с двумя членами: `video` и `audio`, описывающие запрашиваемые медиатипы. Один или оба должны определяться разработчиком. Если браузер не сможет обнаружить медиатреки определяемого типа, которые соответствуют переданным ограничениям, возвращаемый методом промис отменяется с объектом `NotFoundError`.

    Следующий отрывок кода запрашивает и `audio` и `video` типы без дополнительных условий:

    ```js
    { audio: true, video: true }
    ```

    Если определяется значение `true` для медиатипа, результирующий поток обязательно будет иметь в себе запрошенный медиатип. Если ни один из типов не включается в запрос, вызов метода `getUserMedia()` приведёт к ошибке.

    Если полная информация о камерах и микрофонах пользователя недоступна по причинам конфиденциальности, приложение может запросить доступ только к необходимым ему функциям, используя дополнительные условия. Следующий пример запрашивает видеотрек с разрешением камеры 1280x720 пикселей:

    ```js
    {
      audio: true,
      video: { width: 1280, height: 720 }
    }
    ```

    Браузер попытается выполнить условие, но может вернуть видеотрек другого разрешения, если установленные требования невозможно удовлетворить (камера не обладает возможностью такого разрешения), или пользователь переопределяет условие.

    Для минимального, максимального и точного определения значения можно использовать ключевые слова `min`, `max`, или `exac`. Следующий пример запрашивает минимальное разрешение камеры 1280x720:

    ```js
    {
      audio: true,
      video: {
        width: { min: 1280 },
        height: { min: 720 }
      }
    }
    ```

    Если камера не в состоянии обеспечить указанное минимальное разрешение или более высокое, возвращаемый промис будет отменён с объектом `OverconstrainedError`, и пользователь не увидит диалога запроса разрешения.

    Различие поведения происходит по причине того, что ключевые слова `min`, `max`, и `exact` являются обязательными к выполнению. В то время как простые значения и ключевое слово `ideal` - не обязательные к выполнению. Ниже, полный пример:

    ```js
    {
      audio: true,
      video: {
        width: { min: 1024, ideal: 1280, max: 1920 },
        height: { min: 776, ideal: 720, max: 1080 }
      }
    }
    ```

    Значение свойства `ideal` имеет приоритет. Это означает, что браузер будет пытаться обнаружить настройки с наименьшей [fitness distance](https://w3c.github.io/mediacapture-main/#dfn-fitness-distance) (удовлетворяющей разницей) от переданных идеальных значений. Т.е. пытается найти самое близкое к переданному значение.

    Простые значения работают как идеальные, поэтому один из первых примеров выше можно переписать, используя свойство `ideal`:

    ```js
    {
      audio: true,
      video: {
        width: { ideal: 1280 },
        height: { ideal: 720 }
      }
    }
    ```

    Не все значения ограничений являются числовыми. К примеру, на мобильных устройствах следующий отрывок будет запрашивать переднюю камеру (если присутствует) , поэтому в свойстве `facingMode` указано строковое значение `user`:

    ```js
    { audio: true, video: { facingMode: "user" } }
    ```

    Запрос обратной (задней) камеры:

    ```js
    { audio: true, video: { facingMode: { exact: "environment" } } }
    ```

    Следующее строковое свойство - `deviceId` (идентификатор устройства). Его значение может быть получено из метода {{domxref("mediaDevices.enumerateDevices()")}}, возвращающего список, имеющихся на машине устройств, с их идентификаторами, и может быть использовано для запроса определённого устройства по идентификатору этого устройства:

    ```js
    { video: { deviceId: идентификаторНужнойКамеры } }
    ```

    Код выше вернёт запрашиваемую камеру или другую камеру, если требуемая камера недоступна. Для получения доступа к потоку только определённой камеры, без альтернативы, используется свойство `exact` (точно) :

    ```js
    { video: { deviceId: { exact: идентификаторНужнойКамеры } } }
    ```

### Возвращаемое значение

Промис {{jsxref("Promise")}}, при удачном запросе требуемого медиа устройства разрешается объектом типа {{domxref("MediaStream")}} .

### Исключения (Ошибки)

При неудачном запросе медиа устройства, возвращаемый промис {{jsxref("Promise")}} отменяется объектом типа ошибки {{domxref("DOMException")}}. Возможными ошибками могут быть типы:

- `AbortError (Прервано)`
  - : Хотя пользователь и операционная система предоставили доступ к аппаратному устройству, и не возникло проблем с оборудованием, которые могли бы вызвать `NotReadableError`, возникла некоторая проблема, которая не позволила использовать устройство.

<!---->

- `NotAllowedError (Доступ не разрешён)`

  - : Возникает если, одно или несколько запрашиваемых устройств не может быть использованы в настоящее время. Это происходит тогда, когда контекст браузера является не безопасным (страница была загружена используя протокол HTTP вместо HTTPS), а также, если пользователь не разрешил доступ текущему экземпляру браузера к устройству, пользователь отказал в доступе в текущей сессии, или пользователь отказал в доступе к медиаустройствам глобально. Для браузеров, которые поддерживают управление медиаразрешениями с помощью [Feature Policy](/ru/docs/Web/HTTP/Feature_Policy), такая ошибка возвращается если Feature Policy не сконфигурирована для разрешение доступа к медиаустройству или устройствам

    > **Примечание:** Более старые версии спецификации использовали вместо этого SecurityError. `SecurityError` имеет новое значение.

- `NotFoundError (Не найдено)`
  - : Возникает если, типы медиа треков, удовлетворяющие переданным значениям, не найдены.
- `NotReadableError (Не читается)`
  - : Хотя пользователь и предоставил разрешение на использование соответствующих устройств, произошла аппаратная ошибка на уровне операционной системы, браузера или веб-страницы, которая препятствовала доступу к устройству..
- `OverconstrainedError (за границами ограничений)`

  - : Возникает если, в результате указанных ограничений не было найдено устройств, отвечающих запрошенным критериям. Ошибка является объектом типа `OverconstrainedError` и имеет свойство `constraint`, строковое значение которого является именем ограничения, которое было невозможно встретить, и свойство `message`, содержащее читаемую человеком строку, объясняющую проблему.

    > **Примечание:** Ошибка может возникнуть даже, если пользователь ещё не выдал разрешение на использование устройства, использующиеся как поверхность для идентификации отпечатка пальца.

- `SecurityError (ошибка безопасности)`
  - : Возникает если, медиа поддержка отключена в {{domxref("Document")}} на котором был вызван метод `getUserMedia()`. Механизм по которому медиа поддержка включается и отключается находиться в компетенции браузера пользователя.
- `TypeError (ошибка типа)`
  - : Возникает если, список ограничений пустой или все ограничения установлены в `false`. Так же это происходит, если пытаться вызвать метод `getUserMedia()` в небезопасном контексте, поскольку в нем {{domxref("navigator.mediaDevices")}} равно `undefined`.

## Конфиденциальность и безопасность

Поскольку API могут существенно затрагивать вопросы конфиденциальности, спецификация `getUserMedia()` предъявляет широкий спектр требований защиты конфиденциальности и безопасности, которым современные браузеры обязаны следовать.

`getUserMedia()` - это мощная функция, которая может быть использована только в [безопасном контексте](/ru/docs/Web/Security/Secure_Contexts) . В небезопасном контексте, `navigator.mediaDevices` равно `undefined`, предотвращая доступ к методу `getUserMedia()`. Безопасный контекст - это, если кратко, страница, загружаемая по протоколу HTTPS или `file:///` URL схеме, или страница, загружаемая из `localhost`.

В нем обязательно запрашивается пользовательское разрешение к доступу `audio` или `video` источникам. Только контекст документа верхнего уровня, проверенного источника может запросить доступ, используя метод `getUserMedia()`. Если контексту верхнего уровня явно не даётся разрешение для данного {{HTMLElement("iframe")}} используя [Feature Policy](/ru/docs/Web/HTTP/Feature_Policy), пользователю никогда не будет предложено выдать разрешение на использование устройств, пока пользователь самостоятельно не отменит запрет в настройках браузера.

Дополнительные подробности на тему требований и правил, и как они отражены в контексте выполняемого кода, о том, как браузеры управляют вопросами конфиденциальности и безопасности читайте далее.

### Конфиденциальность пользователя

Поскольку, программный интерфейс затрагивает вопросы конфиденциальности и безопасности, в спецификации, для метода `getUserMedia()` содержатся особенные требования для управления уведомлениями и разрешениями пользователя. Сначала метод `getUserMedia()` должен всегда запрашивать пользовательское разрешение до начала сбора данных потоков камеры и микрофона. Браузер может использовать функциональность одного разрешения на домен, но в первый раз браузер обязательно должен получить разрешение пользователя.

Не менее важны правила уведомления. Браузеры обязательно отображают индикатор, который указывает на использование камеры или микрофона, выше или сверху любого, уже имеющегося индикатора аппаратного средства (если присутствует). Они также должны отображать индикатор того, что разрешение уже выдано для использования устройств, даже, если устройства не используются в настоящий момент.

К примеру, в Firefox, в панели адреса отображается красная пульсирующая иконка, указывающая на использование аппаратного средства. Серая иконка указывает на выданное разрешение, но не использование аппаратного средства в данный момент. Физический (явный) свет устройства указывает на текущее использование (активизацию) аппаратного средства. Если отключить камеру программно, свет активности камеры отключится, указывая на то, что она запись не производит, без отмены разрешение на использования, и включиться после запуска камеры в работу.

### Безопасность

Существуют несколько способов управлением безопасностью и контролем в {{Glossary("user agent")}}. Для этого можно использовать метод `getUserMedia()` , который возвращает объекты ошибок, относящиеся к безопасности.

> **Примечание:** **Примечание :** Модель безопасности для метода `getUserMedia()` находиться в процессе разработки. Первоначально спроектированный механизм безопасности находиться в процессе замещения Feature Policy, поэтому различные браузеры имеют разный уровень поддержки безопасности, используют различные механизмы. Вам необходимо осторожно тестировать свой код на различных устройствах и браузерах, чтобы удостовериться в его уверенной работоспособности.

#### Feature Policy (Функциональная политика)

Функция управление безопасностью ([Feature Policy](/ru/docs/Web/HTTP/Feature_Policy)) протокола {{Glossary("HTTP")}} находиться в процессе введения в браузеры, с поддержкой, доступной в различной степени во многих браузерах (но не всегда включённой в настройках по умолчанию, как в Firefox). Метод `getUserMedia()` - один из методов, требующий использования функциональной политики и вашему коду нужно быть готовым к работе с ним. К примеру, чтобы метод был доступен в документах не высокого уровня, разработчику нужно использовать либо атрибут {{htmlattrxref("allow", "iframe")}} на элементе {{HTMLElement("iframe")}} , который использует `getUserMedia()`, либо {{HTTPHeader("Feature-Policy")}} заголовок для страниц , передающихся с сервера, которые используют `getUserMedia()`.

Два разрешения, которые обращаются к `getUserMedia()` - `camera` и `microphone`.

К примеру, строка HTTP заголовка позволит использовать камеру документу и любым встроенные {{HTMLElement("iframe")}} элементам, которые загружаются из одного источника:

```
Feature-Policy: camera 'self'
```

Строка ниже, запрашивает доступ к микрофону для текущего источника и указанном в заголовке источнике `https://developer.mozilla.org`:

```
Feature-Policy: microphone 'self' https://developer.mozilla.org
```

Если используется `getUserMedia()` внутри элемента `<iframe>`, можно запросить разрешение только для этого фрейма, которое явно более безопаснее, чем запрашивать более общее разрешение. Здесь нам нужно использовать и камеру и микрофон:

```html
<iframe src="https://mycode.example.net/etc" allow="camera;microphone">
</iframe>
```

Прочитайте наше руководство [Применение функциональной политики](/ru/docs/Web/HTTP/Feature_Policy/Using_Feature_Policy), изучив подробнее то, как это работает.

#### Безопасность на основе шифрования

Метод `getUserMedia()` доступен _только_ для [безопасных контекстов](/ru/docs/Web/Security/Secure_Contexts). Безопасный контекст - это уверенность браузера в том, что документ был загружен безопасно, используя HTTPS/TLS, и имеет ограниченную подверженность небезопасным контекстам. Если документ не загружен в безопасном контексте, свойство {{domxref("navigator.mediaDevices")}} равно `undefined`, делая невозможным доступ к методу `getUserMedia()`. Попытка получить доступ в такой ситуации приведёт к ошибке `TypeError`.

#### Безопасность источника документа

Существуют несколько небезопасных способа загрузить документ, который может попытаться вызвать метод `getUserMedia()`. Ниже представлены примеры ситуаций, в которых `getUserMedia()` не разрешается вызывать:

- Документ, загруженный в песочницу {{HTMLElement("iframe")}} элемента не может вызвать `getUserMedia()`, до тех пор пока, на элементе `<iframe>` находиться атрибут {{htmlattrxref("sandbox", "iframe")}}, установленный в значение `allow-same-origin`.
- Документ, загруженный по протоколам `data://` или `blob://` в URL-адресе, не имеющий источника (такими являются типы URL-ов, введённые пользователями в строке адреса браузера) не может вызвать `getUserMedia()`. Подобные типы URL-ов, загружаемые из JavaScript-кода, наследуют разрешения скрипта.
- Иные ситуации, документы которых не имеют источника, к примеру элемент, содержащий атрибут {{htmlattrxref("srcdoc", "iframe")}}, использующийся для указания содержимого фрейма.

## Примеры

### Ширина и высота

Этот пример выбирает указанное разрешение камеры и присваивает ссылку на объект {{domxref("MediaStream")}} свойству `srcObject` элемента `video` .

```js
// Выбирает разрешение камеры близкое к 1280x720.
var constraints = { audio: true, video: { width: 1280, height: 720 } };

navigator.mediaDevices.getUserMedia(constraints)
.then(function(mediaStream) {
  var video = document.querySelector('video');
  video.srcObject = mediaStream;
  video.onloadedmetadata = function(e) {
    video.play();
  };
})
.catch(function(err) { console.log(err.name + ": " + err.message); }); // always check for errors at the end.
```

### Использование новых API в старых браузерах

Ниже, находятся примеры, использующие `navigator.mediaDevices.getUserMedia()`, с полифилами для работы в старых браузерах. Обратите внимание, что эти полифилы не корректируют все различия в синтаксисе, и не работают во всех браузерах. Рекомендуется использовать библиотеку [adapter.js](https://github.com/webrtc/adapter) , как производственный полифил.

```js
// Старые браузеры могут не реализовывать свойство mediaDevices,
// поэтому сначала присваиваем свойству ссылку на пустой объект

if (navigator.mediaDevices === undefined) {
  navigator.mediaDevices = {};
}

// Некоторые браузеры частично реализуют свойство mediaDevices, поэтому
//мы не можем присвоить ссылку на объект свойству getUserMedia, поскольку
//это переопределит существующие свойства. Здесь, просто добавим свойство
//getUserMedia , если оно отсутствует.

if (navigator.mediaDevices.getUserMedia === undefined) {
  navigator.mediaDevices.getUserMedia = function(constraints) {

    // Сначала, если доступно, получим устаревшее getUserMedia

  var getUserMedia = navigator.webkitGetUserMedia || navigator.mozGetUserMedia;

   //Некоторые браузеры не реализуют его, тогда вернём отменённый промис
   // с ошибкой для поддержания последовательности интерфейса

    if (!getUserMedia) {
      return Promise.reject(new Error('getUserMedia is not implemented in this browser'));
    }

    // Иначе, обернём промисом устаревший navigator.getUserMedia

    return new Promise(function(resolve, reject) {
      getUserMedia.call(navigator, constraints, resolve, reject);
    });
  }
}

navigator.mediaDevices.getUserMedia({ audio: true, video: true })
.then(function(stream) {
  var video = document.querySelector('video');
  // Устаревшие браузеры могут не иметь свойство srcObject
  if ("srcObject" in video) {
    video.srcObject = stream;
  } else {
    // Не используем в новых браузерах
    video.src = window.URL.createObjectURL(stream);
  }
  video.onloadedmetadata = function(e) {
    video.play();
  };
})
.catch(function(err) {
  console.log(err.name + ": " + err.message);
});
```

### Частота кадров

В некоторых случаях может быть желательна более низкая частота кадров, например, при передаче WebRTC с ограничениями полосы пропускания

```js
var constraints = { video: { frameRate: { ideal: 10, max: 15 } } };
```

### Передняя и задняя камеры

На мобильных устройствах.

```js
var front = false;
document.getElementById('flip-button').onclick = function() { front = !front; };

var constraints = { video: { facingMode: (front? "user" : "environment") } };
```

## Спецификации

{{Specifications}}

## Совместимость с браузерами

{{Compat}}

## Смотрите также

- Предыдущее {{domxref("navigator.getUserMedia()")}} API.
- {{domxref("mediaDevices.enumerateDevices()")}}: Перечисление доступных медиа устройств
- [WebRTC API](/ru/docs/Web/API/WebRTC_API)
- [Media Capture and Streams API (Media Streams)](/ru/docs/Web/API/Media_Streams_API)
- [Screen Capture API](/ru/docs/Web/API/Screen_Capture_API): Захват области экрана как {{domxref("MediaStream")}}
- {{domxref("mediaDevices.getDisplayMedia()")}}: Получение потока, содержащего экранную область
- [Taking webcam photos](/ru/docs/Web/API/WebRTC_API/Taking_still_photos): Руководство по использованию `getUserMedia()` для получения снимком вместо видео.