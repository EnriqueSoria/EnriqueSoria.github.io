---                                                                             
title: 'Making child serializer "Source" attributes work with ListSerializer'
date: 2023-04-03T17:07:00+01:00
type: 'post'
layout: 'single'
tags: ['drf', 'django', 'python', 'english']
draft: false
---


{{< gist EnriqueSoria eaffaae1687db592e3f9f60baed3bd11 >}}


Suppose we have a model Product that has a ManyToMany relationship with a Category model. The Category model has a field name which we want to include in the serialized representation of the Product model's categories.

```python
class Category(models.Model):
    name = models.CharField(max_length=100)

class Product(models.Model):
    name = models.CharField(max_length=100)
    categories = models.ManyToManyField(Category)
```


We could define a serializer for the Product model as follows, using the ChildSourceListSerializer for the categories field:

```python
class ProductSerializer(serializers.ModelSerializer):
    categories = ChildSourceListSerializer(child=serializers.CharField(source="name"))

    class Meta:
        model = Product
        fields = ['name', 'categories']
```

To test this serializer, we can create some Category and Product instances and serialize them:

```python
# create some categories
category1 = Category.objects.create(name='category1')
category2 = Category.objects.create(name='category2')

# create a product and add categories to it
product = Product.objects.create(name='product1')
product.categories.add(category1, category2)

# serialize the product
serializer = ProductSerializer(product)
serialized_data = serializer.data

# print the serialized data
print(serialized_data)
```

This should output:

```python
{
    'name': 'product1',
    'categories': ['category1', 'category2']
}
```

Note that the categories field is serialized as a list of category names, as specified by the ChildSourceListSerializer in the serializer definition.
