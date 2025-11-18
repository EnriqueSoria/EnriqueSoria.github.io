---
title: "How to display a JSON value in Django admin (when using MySQL)"
date: 2025-11-18T18:24:02+01:00
type: 'post'
layout: 'single'
images: ["https://enriquesoria.github.io/how-to-display-a-json-value-in-django-admin-when-using-mysql.png"]
tags: ['python', 'english', 'django', 'admin', 'mysql']
draft: false
---


> tldr; here's a small working example of this: https://github.com/EnriqueSoria/DisplayJsonValueInAdmin


Let's say that, for some reason, we have a json stored in a `TextField` or a `JSONField` ([available since Django 3.1](https://docs.djangoproject.com/en/3.1/ref/models/fields/#jsonfield)) that stores a payload. 

```python
class Webhook(models.Model):
    payload = models.TextField()  # works also with models.JSONField()
    ... # more fields
```

## Showing the `timestamp` as a `datetime` in the admin list

If we wanted to display the `timestamp` in the admin list, we could use some of the methods present in [this guide](https://books.agiliq.com/projects/django-admin-cookbook/en/latest/calculated_fields.html). For instance, we could create a method in the admin:

```python
@admin.register(Webhook)
class WebhookAdmin(admin.ModelAdmin):
    list_display = ("id", "payload_timestamp")

    def payload_timestamp(self, obj) -> datetime.datetime:
        payload_as_dict = json.loads(obj.payload)
        return datetime.datetime.fromisoformat(payload_as_dict["timestamp"])
```

<img width="920" height="477" alt="imatge" src="https://github.com/user-attachments/assets/cc800816-b7c8-4324-8cf3-502761f467c6" />


## Annotating the `timestamp` to allow sorting in the admin list 

But, what happened if we wanted to [enable sorting on this calculated field](https://books.agiliq.com/projects/django-admin-cookbook/en/latest/sorting_calculated_fields.html)? Then we would have to annotate that field in the queryset. Here it comes the tricky part!

To extract a value from a json in MySQL we have the [`JSON_EXTRACT`](https://dev.mysql.com/doc/refman/8.4/en/json-search-functions.html#function_json-extract) function, which is not implemented by the Django ORM, but can be used with [`Func`](https://docs.djangoproject.com/en/5.2/ref/models/expressions/#django.db.models.Func):

```python
@admin.register(Webhook)
class WebhookAdmin(admin.ModelAdmin):
    list_display = ("id", "payload_timestamp")

    @admin.display(description="Timestamp", ordering="_payload_timestamp")
    def payload_timestamp(self, obj):
        return obj._payload_timestamp

    def get_queryset(self, request):
        queryset = super().get_queryset(request)
        return queryset.annotate(
            _payload_timestamp=Func(
                "payload",
                Value("$.timestamp"),
                function="JSON_EXTRACT",
                output_field=CharField(),
            ),
        )
```

...or, using [django-mysql's `JSONExtract`](https://github.com/adamchainz/django-mysql):
```python
from django_mysql.models.functions import JSONExtract

queryset.annotate(
    _payload_timestamp=JSONExtract("payload", "$.timestamp"),
)
```

<img width="920" height="477" alt="imatge" src="https://github.com/user-attachments/assets/32f15f5b-8375-4b16-9c89-d45fa35ed2ce" />

...as we can see, that could do the trick, but we can go further and try to unquote the `timestamp` and convert it to a `datetime`.

## Casting the annotated `timestamp` as a `DateTimeField`

Before casting the value to a `DateTimeField()`, we need to unquote it. To do this we can leverage the [`JSON_UNQUOTE` MySQL function](https://dev.mysql.com/doc/refman/8.4/en/json-modification-functions.html#function_json-unquote) and the ORM-provided [`Cast`](https://docs.djangoproject.com/en/3.1/ref/models/database-functions/#cast) function. (unfortunately, we don't have `JSON_UNQUOTE` implemented in `django-mysql`)

```python
@admin.register(Webhook)
class WebhookAdmin(admin.ModelAdmin):
    list_display = ("id", "payload_timestamp")

    @admin.display(description="Timestamp", ordering="_payload_timestamp")
    def payload_timestamp(self, obj) -> datetime.datetime:
        return obj._payload_timestamp

    def get_queryset(self, request):
        queryset = super().get_queryset(request)
        extracted_timestamp = Func(
            "payload",
            Value("$.timestamp"),
            function="JSON_EXTRACT",
            output_field=CharField(),
        )
        unquoted_timestamp = Func(
            extracted_timestamp,
            function="JSON_UNQUOTE",
        )
        cast_to_datetime_timestamp = Cast(
            unquoted_timestamp,
            output_field=DateTimeField(),
        )
        return queryset.annotate(_payload_timestamp=cast_to_datetime_timestamp)
```

<img width="920" height="546" alt="imatge" src="https://github.com/user-attachments/assets/1e523ed4-e1ca-4a58-bcf8-c23de24e0729" />

Now we could even use this annotated `_payload_timestamp` to filter the queryset or annotate derivated values (i.e. `.annotate(year=_payload_timestamp__year)`), but unfortunately we can't use it as `date_hierarchy` or `list_fields` in the admin.

## Encapsulating this functions into a single one: `JSONExtractAndCast`

We can put all of this together into a single, reusable, piece of code:

{{< gist EnriqueSoria ff09cdfe007a0380add58d9331b7523d >}}

...and our admin code would look like this:
```python
@admin.register(Webhook)
class WebhookAdmin(admin.ModelAdmin):
    list_display = ("id", "payload_timestamp")

    @admin.display(description="Timestamp", ordering="_payload_timestamp")
    def payload_timestamp(self, obj) -> datetime.datetime:
        return obj._payload_timestamp

    def get_queryset(self, request):
        queryset = super().get_queryset(request)
        return queryset.annotate(
            _payload_timestamp=JSONExtractAndCast(
                "payload",
                "$.timestamp",
                output_field=DateTimeField(),
            )
        )
```

<img width="920" height="477" alt="imatge" src="https://github.com/user-attachments/assets/977bd4b0-a88c-4bb1-85b9-664eced6a91d" />


