---
title: "Hackyeah"
date: 2021-10-16T00:00:10+02:00
description: "Django - quick tips - Start"
tags:
- Python
- Django
series:
- Django - quick tips
categories:
- Django
featured_image: "ikony_tematow/programming.png"
---
### Django - Quick Tips

Rozpoczynam nową serię z drobnymi poradami dotyczącymi Django.
Na początek serii napiszę i omówię dość przydatny templatetag, który ułatwi pracę z paginacją i różnym filtrowaniem/sortowaniem na stronie.

**Problem** 

Na stronie masz paginator, a także sortowanie, bądź filtrowanie treści. 
Klikasz, by filtrować treść i chcesz przejść na kolejną stronę, a tu... jesteś co prawda na kolejnej stronie, ale bez wybranego filtrowania.


### Template Tag

**Przygotowanie** 

W folderze aplikacji django, gdzie są pliki m.in. `views.py`, `models.py` tworzymy moduł, czyli tworzymy folder `templatetags` a w nim plik `__init__.py`, oraz `url_tag.py`.

**Edycja `url_tag.py`**

``` python
from django import template
register = template.Library()


@register.simple_tag(takes_context=True)
def url_add(context, **kwargs):
    request = context.get('request')
    get = request.GET.copy()
    for kwarg in kwargs:
        get.pop(kwarg, None)
    get.update(kwargs)
    path = f"{request.path}?"
    path += get.urlencode()
    return path
```

**Użycie tagu**

By użyć tagu `{% url_add %}`, musimy go zarejestrować na początku templatu

```
{% load url_tag %}
```

Następnie w przypadku paginacji zamiast:
```html
<a href="{% url_add page=i %}"
```
wstawiamy:
```html
<a href="{% url_add page=i %}"
```

Jeśli chcemy powiązać tag z formularzem, by np. łączyć zapytania z filtrowaniem, bądź sortowaniem, to podajemy tag w `form action`:

```html
<form action="{% url_add %}"
```

Dzięki temu adres url może wyglądać następująco:
```url
https://moja-strona.pl/news/?category=3&category=4&page=2
```