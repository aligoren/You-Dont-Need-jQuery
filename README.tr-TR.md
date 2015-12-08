## jQuery'ye İhtiyacınız Yok

Son zamanlarda Frontend geliştirme ortamları hızla gelişiyor, modern tarayıcılar şu an zaten yeterince iyi DOM/BOM API'ları sağlamış durumda. Artık DOM manipülasyonu ve eventleri için bir de jQuery öğrenmek zorunda değiliz. Aynı zamanda, React, Angular ve Vue gibi yaygın frameworkler sayesinde, artık direkt DOM manipülasyonu yapmak, kaçınılması gereken bir şeye dönüştü, ve jQuery daha önce hiç bu kadar önemsizleşmemişti. Bu projede, en yaygın jQuery methodlarının, IE 10+'un desteklediği saf javascript alternatiflerini bulabilirsiniz.

## İçindekiler

1. [Query Selector](#query-selector)
1. [CSS & Style](#css-style)
1. [DOM Manipulation](#dom-manipulation)
1. [Ajax](#ajax)
1. [Events](#events)
1. [Utilities](#utilities)
1. [Translation](#translation)
1. [Browser Support](#browser-support)

## Query Selector

Class, id, atrribute gibi yaygın seçiciler yerine `document.querySelector` veya `document.querySelectorAll` kullanabiliriz. Aralarındaki fark:
* `document.querySelector` eşleşen ilk elemanı döndürür.
* `document.querySelectorAll` eşleşen tüm elemanları NodeList olarak döndürür. Array'e çevirmek için `[].slice.call` kullanılabilir
* Eşleşen eleman yoksa, jQuery `[]` döndürür, DOM API ise `null`. Null Pointer Exception'a dikkat etmelisiniz.

> Uyarı: `document.querySelector` ve `document.querySelectorAll` epey **YAVAŞ**, Performans getirisi istiyorsanız mümkün oldukça `getElementById`, `document.getElementsByClassName` veya `document.getElementsByTagName` kullanın.

- [1.0](#1.0) <a name='1.0'></a> Seçici ile sorgulama

  ```js
  // jQuery
  $('selector');

  // Native
  document.querySelectorAll('selector');
  ```

- [1.1](#1.1) <a name='1.1'></a> Sınıfa göre sorgulama

  ```js
  // jQuery
  $('.class');

  // Native
  document.querySelectorAll('.class');
  
  // veya
  document.getElementsByClassName('class');
  ```

- [1.2](#1.2) <a name='1.2'></a> Id'ye göre sorgulama

  ```js
  // jQuery
  $('#id');

  // Native
  document.querySelector('#id');
  
  // veya
  document.getElementById('id');
  ```

- [1.3](#1.3) <a name='1.3'></a> Attribute'a göre sorgulama

  ```js
  // jQuery
  $('a[target=_blank]');

  // Native
  document.querySelectorAll('a[target=_blank]');
  ```

- [1.4](#1.4) <a name='1.4'></a> Bir şey bulma.

  + Eleman bulma

    ```js
    // jQuery
    $el.find('li');

    // Native
    el.querySelectorAll('li');
    ```

  + Body'yi bulma

    ```js
    // jQuery
    $('body');

    // Native
    document.body;
    ```

  + Özelliği (atrribute) bulma

    ```js
    // jQuery
    $el.attr('foo');

    // Native
    e.getAttribute('foo');
    ```

  + Veri özelliği (data attribute) bulma

    ```js
    // jQuery
    $el.data('foo');

    // Native
    // getAttribute kullanımı
    el.getAttribute('data-foo');
    // `dataset` de kullanabilirsiniz (IE 11+ destekler)
    el.dataset['foo'];
    ```

- [1.5](#1.5) <a name='1.5'></a> Kardeş/Önceki/Sonraki Elemanlar

  + Kardeş elemanlar

    ```js
    // jQuery
    $el.siblings();

    // Native
    [].filter.call(el.parentNode.children, function(child) {
      return child !== el;
    });
    ```

  + Önceki elemanlar

    ```js
    // jQuery
    $el.prev();

    // Native
    el.previousElementSibling;

    ```

  + Sonraki elemanlar

    ```js
    // next
    $el.next();
    el.nextElementSibling;
    ```

- [1.6](#1.6) <a name='1.6'></a> Closest (En yakın)

  Elemanın kendisinden kök elemana doğru tüm kapsayıcılarından, aranılan kritere uygun ilk elemanı döndürür.

  ```js
  // jQuery
  $el.closest(queryString);

  // Native
  function closest(el, selector) {
    const matchesSelector = el.matches || el.webkitMatchesSelector || el.mozMatchesSelector || el.msMatchesSelector;

    while (el) {
      if (matchesSelector.call(el, selector)) {
        return el;
      } else {
        el = el.parentElement;
      }
    }
    return null;
  }
  ```

- [1.7](#1.7) <a name='1.7'></a> Parents Until (Eşleşene kadarki kapsayıcılar)

  Elemanın, belirtilen kritere uygun "En yakın kapsayıcı"sına kadar olan tüm kapsayıcıları (eşleşen hariç) döndür. Arama kriteri seçici, DOM elemanı veya jQuery nesnesi olabilir.

  ```js
  // jQuery
  $el.parentsUntil(selector, filter);

  // Native
  function parentsUntil(el, selector, filter) {
    const result = [];
    const matchesSelector = el.matches || el.webkitMatchesSelector || el.mozMatchesSelector || el.msMatchesSelector;

    // match start from parent
    el = el.parentElement;
    while (el && !matchesSelector.call(el, selector)) {
      if (!filter) {
        result.push(el);
      } else {
        if (matchesSelector.call(el, filter)) {
          result.push(el);
        }
      }
      el = el.parentElement;
    }
    return result;
  }
  ```

- [1.8](#1.8) <a name='1.8'></a> Form

  + Input/Textarea

    ```js
    // jQuery
    $('#my-input').val();

    // Native
    document.querySelector('#my-input').value;
    ```

  + e.currentTarget'ın `.radio` seçicisiyle eşleşen elemanlar arasındaki sırası

    ```js
    // jQuery
    $(e.currentTarget).index('.radio');

    // Native
    [].indexOf.call(document.querySelectAll('.radio'), e.currentTarget);
    ```

- [1.9](#1.9) <a name='1.9'></a> Iframe İçeriği

  `$('iframe').contents()` bu iframe'in `contentDocument`'ini döndürür.

  + Iframe içeriği

    ```js
    // jQuery
    $iframe.contents();

    // Native
    iframe.contentDocument;
    ```

  + Iframe içinde sorgu

    ```js
    // jQuery
    $iframe.contents().find('.css');

    // Native
    iframe.contentDocument.querySelectorAll('.css');
    ```

**[⬆ başa dön](#table-of-contents)**

## CSS & Style

- [2.1](#2.1) <a name='2.1'></a> CSS

  + Stili getir

    ```js
    // jQuery
    $el.css("color");

    // Native
    // NOT: Stil değeri 'auto' ise 'auto' döndürür.
    const win = el.ownerDocument.defaultView;
    // null, sözde sitilleri döndürme demek.
    win.getComputedStyle(el, null).color;
    ```

  + Stil ata

    ```js
    // jQuery
    $el.css({ color: "#ff0011" });

    // Native
    el.style.color = '#ff0011';
    ```

  + Birden fazla stil ata/getir

    Note that if you want to set multiple styles once, you could refer to [setStyles](https://github.com/oneuijs/oui-dom-utils/blob/master/src/index.js#L194) method in oui-dom-utils package.


  + Sınıf (class) ekle

    ```js
    // jQuery
    $el.addClass(className);

    // Native
    el.classList.add(className);
    ```

  + Sınıf sil

    ```js
    // jQuery
    $el.removeClass(className);

    // Native
    el.classList.remove(className);
    ```

  + Sınıf varlık kontrolü

    ```js
    // jQuery
    $el.hasClass(className);

    // Native
    el.classList.contains(className);
    ```

  + Sınıf ekle-çıkar (yoksa ekle varsa çıkar)

    ```js
    // jQuery
    $el.toggleClass(className);

    // Native
    el.classList.toggle(className);
    ```

- [2.2](#2.2) <a name='2.2'></a> Genişlik & Yükseklik

  Genişlik ve yükseklik teoride aynılar, örnek olarak yüksekliğe bakalım:

  + Pencere (window) yüksekliği

    ```js
    // window height
    $(window).height();
    // without scrollbar, behaves like jQuery
    window.document.documentElement.clientHeight;
    // with scrollbar
    window.innerHeight;
    ```

  + Doküman (document) yüksekliği

    ```js
    // jQuery
    $(document).height();

    // Native
    document.documentElement.scrollHeight;
    ```

  + Eleman yüksekliği

    ```js
    // jQuery
    $el.height();

    // Native
    function getHeight(el) {
      const styles = this.getComputedStyles(el);
      const height = el.offsetHeight;
      const borderTopWidth = parseFloat(styles.borderTopWidth);
      const borderBottomWidth = parseFloat(styles.borderBottomWidth);
      const paddingTop = parseFloat(styles.paddingTop);
      const paddingBottom = parseFloat(styles.paddingBottom);
      return height - borderBottomWidth - borderTopWidth - paddingTop - paddingBottom;
    }
    // accurate to integer（when `border-box`, it's `height`; when `content-box`, it's `height + padding + border`）
    el.clientHeight;
    // accurate to decimal（when `border-box`, it's `height`; when `content-box`, it's `height + padding + border`）
    el.getBoundingClientRect().height;
    ```

- [2.3](#2.3) <a name='2.3'></a> Position & Offset

  + Position

    ```js
    // jQuery
    $el.position();

    // Native
    { left: el.offsetLeft, top: el.offsetTop }
    ```

  + Offset

    ```js
    // jQuery
    $el.offset();

    // Native
    function getOffset (el) {
      const box = el.getBoundingClientRect();

      return {
        top: box.top + window.pageYOffset - document.documentElement.clientTop,
        left: box.left + window.pageXOffset - document.documentElement.clientLeft
      }
    }
    ```

- [2.4](#2.4) <a name='2.4'></a> Scroll Top

  ```js
  // jQuery
  $(window).scrollTop();

  // Native
  (document.documentElement && document.documentElement.scrollTop) || document.body.scrollTop;
  ```

**[⬆ başa dönendi](#table-of-contents)**

## DOM Manipulation

- [3.1](#3.1) <a name='3.1'></a> Remove
  ```js
  // jQuery
  $el.remove();

  // Native
  el.parentNode.removeChild(el);
  ```

- [3.2](#3.2) <a name='3.2'></a> Text

  + Get text

    ```js
    // jQuery
    $el.text();

    // Native
    el.textContent;
    ```

  + Set text

    ```js
    // jQuery
    $el.text(string);

    // Native
    el.textContent = string;
    ```

- [3.3](#3.3) <a name='3.3'></a> HTML

  + Get HTML

    ```js
    // jQuery
    $el.html();

    // Native
    el.innerHTML;
    ```

  + Set HTML

    ```js
    // jQuery
    $el.html(htmlString);

    // Native
    el.innerHTML = htmlString;
    ```

- [3.4](#3.4) <a name='3.4'></a> Append

  append child element after the last child of parent element

  ```js
  // jQuery
  $el.append("<div id='container'>hello</div>");

  // Native
  let newEl = document.createElement('div');
  newEl.setAttribute('id', 'container');
  newEl.innerHTML = 'hello';
  el.appendChild(newEl);
  ```

- [3.5](#3.5) <a name='3.5'></a> Prepend

  ```js
  // jQuery
  $el.prepend("<div id='container'>hello</div>");

  // Native
  let newEl = document.createElement('div');
  newEl.setAttribute('id', 'container');
  newEl.innerHTML = 'hello';
  el.insertBefore(newEl, el.firstChild);
  ```

- [3.6](#3.6) <a name='3.6'></a> insertBefore

  Insert a new node before the selected elements

  ```js
  // jQuery
  $newEl.insertBefore(queryString);

  // Native
  newEl.insertBefore(document.querySelector(queryString));
  ```

- [3.7](#3.7) <a name='3.7'></a> insertAfter

  Insert a new node after the selected elements

  ```js
  // jQuery
  $newEl.insertAfter(queryString);

  // Native
  function insertAfter(newEl, queryString) {
    const parent = document.querySelector(queryString).parentNode;

    if (parent.lastChild === newEl) {
      parent.appendChild(newEl);
    } else {
      parent.insertBefore(newEl, parent.nextSibling);
    }
  },
  ```

**[⬆ başa dön](#table-of-contents)**

## Ajax

Replace with [fetch](https://github.com/camsong/fetch-ie8) and [fetch-jsonp](https://github.com/camsong/fetch-jsonp)

**[⬆ başa dön](#table-of-contents)**

## Events

For a complete replacement with namespace and delegation, refer to https://github.com/oneuijs/oui-dom-events

- [5.1](#5.1) <a name='5.1'></a> Bind an event with on

  ```js
  // jQuery
  $el.on(eventName, eventHandler);

  // Native
  el.addEventListener(eventName, eventHandler);
  ```

- [5.2](#5.2) <a name='5.2'></a> Unbind an event with off

  ```js
  // jQuery
  $el.off(eventName, eventHandler);

  // Native
  el.removeEventListener(eventName, eventHandler);
  ```

- [5.3](#5.3) <a name='5.3'></a> Trigger

  ```js
  // jQuery
  $(el).trigger('custom-event', {key1: 'data'});

  // Native
  if (window.CustomEvent) {
    const event = new CustomEvent('custom-event', {detail: {key1: 'data'}});
  } else {
    const event = document.createEvent('CustomEvent');
    event.initCustomEvent('custom-event', true, true, {key1: 'data'});
  }

  el.dispatchEvent(event);
  ```

**[⬆ başa dön](#table-of-contents)**

## Utilities

- [6.1](#6.1) <a name='6.1'></a> isArray

  ```js
  // jQuery
  $.isArray(range);

  // Native
  Array.isArray(range);
  ```

- [6.2](#6.2) <a name='6.2'></a> Trim

  ```js
  // jQuery
  $.trim(string);

  // Native
  string.trim();
  ```

- [6.3](#6.3) <a name='6.3'></a> Object Assign

  Extend, use object.assign polyfill https://github.com/ljharb/object.assign

  ```js
  // jQuery
  $.extend({}, defaultOpts, opts);

  // Native
  Object.assign({}, defaultOpts, opts);
  ```

- [6.4](#6.4) <a name='6.4'></a> Contains

  ```js
  // jQuery
  $.contains(el, child);

  // Native
  el !== child && el.contains(child);
  ```

**[⬆ başa dön](#table-of-contents)**

## Çeviriler

* [한국어](./README.ko-KR.md)
* [简体中文](./README.zh-CN.md)
* [Bahasa Melayu](./README-my.md)
* [Adem ilter](./README.tr-TR.md)

## Tarayıcı Desteği

![Chrome](https://raw.github.com/alrra/browser-logos/master/chrome/chrome_48x48.png) | ![Firefox](https://raw.github.com/alrra/browser-logos/master/firefox/firefox_48x48.png) | ![IE](https://raw.github.com/alrra/browser-logos/master/internet-explorer/internet-explorer_48x48.png) | ![Opera](https://raw.github.com/alrra/browser-logos/master/opera/opera_48x48.png) | ![Safari](https://raw.github.com/alrra/browser-logos/master/safari/safari_48x48.png)
--- | --- | --- | --- | --- |
Latest ✔ | Latest ✔ | 10+ ✔ | Latest ✔ | 6.1+ ✔ |

# License

MIT
