---                                                                             
title: 'The story of how `ChildSourceListSerializer` became a thing'
date: 2023-04-03T17:07:00+01:00
type: 'post'
layout: 'single'
tags: ['drf', 'django', 'python', 'english']
draft: false
---

## The story

Once upon a time, there was a Django developer named Sarah who was working on a new API using Django REST Framework. She had a model `Movie` which had a many-to-many relationship with `Genre`. Sarah wanted to create an endpoint that returned a list of movies with their associated genres as a list of strings.

Initially, Sarah tried to use a standard `ListSerializer` to serialize the genres, but she quickly realized that the source attribute of the child serializer was not taken into account when serializing a list of objects. This meant that she was unable to customize the serialization of the child objects based on their source attribute.

Sarah looked for a solution and came across a blog post that described a custom `ListSerializer` subclass called `ChildSourceListSerializer`. This subclass extended the functionality of the standard `ListSerializer` by taking the source attribute of the child serializer into account when serializing a list of objects.

Sarah decided to use `ChildSourceListSerializer` in her implementation, and it worked perfectly. Now, her endpoint returns a list of movies with their associated genres as a list of strings, just as she had wanted.

Sarah was happy that she had found a solution to her problem and decided to share her experience with the Django community. She wrote a blog post about her use of `ChildSourceListSerializer` and how it had helped her solve her serialization problem. The post became very popular and many developers started using ChildSourceListSerializer in their own projects.

From that day on, `ChildSourceListSerializer` became a popular tool among Django developers for customizing the serialization of child objects in a list based on their source attribute. And that's how `ChildSourceListSerializer` became a thing!

## The `ChildSourceListSerializer` code

{{< gist EnriqueSoria eaffaae1687db592e3f9f60baed3bd11 >}}


## The example

### Models
```python
from django.db import models

class Genre(models.Model):
    name = models.CharField(max_length=50)

    def __str__(self):
        return self.name

class Movie(models.Model):
    title = models.CharField(max_length=255)
    year = models.IntegerField()
    genres = models.ManyToManyField(Genre)

    def __str__(self):
        return self.title
```

### Serializer
```python
class MovieSerializer(serializers.ModelSerializer):
    genres = ChildSourceListSerializer(child=serializers.CharField(source="name"))

    class Meta:
        model = Movie
        fields = ['title', 'year', 'genres']
``` 

### Output
```json
[
  {
      "title": "The Shawshank Redemption",
      "year": "1994",
      "genres": ["Drama", "Crime"]
  },
  {
      "title": "The Godfather",
      "year": "1972",
      "genres": ["Drama", "Crime"]
  },
  {
      "title": "The Dark Knight",
      "year": "2008",
      "genres": ["Action", "Crime", "Drama"]
  }
]
```


_Disclaimer: This blog post was generated using ChatGPT_
