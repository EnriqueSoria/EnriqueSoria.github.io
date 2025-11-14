---
title: "How to display a JSON value in Django admin (when using MySQL)"
date: 2025-10-22T20:30:02+02:00
type: 'post'
layout: 'single'
images: ["https://enriquesoria.github.io/avatar-small.jpg"]
tags: ['python', 'english', 'django', 'admin', 'mysql']
draft: true
---

We have a field in a model which is a CharField (but it also works if it's the not-so-new [django's JSONField](https://docs.djangoproject.com/en/dev/ref/models/fields/#jsonfield))

First of all, we're going to annotate the value:
```python
Webhook.objects.annotate(
    _payload_timestamp=Func(
        "payload",
        Value("$.timestamp"),
        function="JSON_EXTRACT",
        output_field=CharField(),
    ),
)
```

Or, using [django-mysql's `JSONExtract`](https://github.com/adamchainz/django-mysql):
```python
from django_mysql.models.functions import JSONExtract

Webhook.objects.annotate(
    _payload_timestamp=JSONExtract("payload", "$.timestamp"),
)
```

We could use `output_field=DateTimeField()` to convert it to a `datetime.datetime`, but it doesn't work because the value is returned with quotes:
```pycon
>>> from django_mysql.models.functions import JSONExtract
>>> Webhook.objects.annotate(
...     _payload_timestamp=JSONExtract("payload", "$.timestamp"),
... ).values_list("_payload_timestamp", flat=True).first()
'"2025-10-21T09:50:36.489317Z"'
```

We can use a MySQL function that unquotes it:
```python
from django_mysql.models.functions import JSONExtract

Webhook.objects.annotate(
    _payload_timestamp=Func(
        JSONExtract("payload", "$.timestamp"),
        function="JSON_UNQUOTE",
        output_field=TextField(),
    ),
)
```

Now we have an unquoted string, but we couldn't have used `output_field=DateTimeField()` yet. But we're able to cast it again into it:
```python
from django_mysql.models.functions import JSONExtract

Webhook.annotate(
    _payload_timestamp=Cast(
        Func(
            JSONExtract("payload", "$.timestamp"),
            function="JSON_UNQUOTE",
            output_field=TextField(),
        ),
        output_field=DateTimeField(),
    )
)
```


```python
class JSONExtractAndCast(Cast):
    def __init__(self, expression, *paths, output_field):
        # borrowed from django_mysql.models.functions.JSONExtract
        exprs = [expression]
        for path in paths:
            if not hasattr(path, "resolve_expression"):
                path = Value(path)
            exprs.append(path)

        extracted_value = Func(*exprs, function="JSON_EXTRACT", output_field=CharField())
        unquoted_value = Func(extracted_value, function="JSON_UNQUOTE", output_field=output_field)
        super().__init__(unquoted_value, output_field=output_field)

Webhook.objects.annotate(
    _payload_timestamp=JSONExtractAndCast("payload", "$.timestamp", output_field=DateTimeField())
)
```


```python
class WebhookAdmin(admin.ModelAdmin):
    list_display = (
        "id",
        "payload_timestamp",
    )

    def get_queryset(self, request):
        return (
            super()
            .get_queryset(request)
            .annotate(
                _payload_timestamp=JSONExtractAndCast("payload", "$.timestamp", output_field=DateTimeField())
            )
        )
```

<img width="1471" height="566" alt="imatge" src="https://github.com/user-attachments/assets/536b56ce-0814-41c3-b0ad-5144317e1a0d" />
<img width="1471" height="566" alt="imatge" src="https://github.com/user-attachments/assets/2f847381-8d7a-47e3-9c8e-6f7fc5009e9f" />

