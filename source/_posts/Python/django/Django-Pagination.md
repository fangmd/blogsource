---
title: Django - Pagination
date: 2016-12-28 13:18:12
tags: [Django, Pagination]
category: Python

---

>Django 实现分页

# 在 View 中使用 Paginator

view class 

```python

import models
from django.views.generic import ListView

class CarListView(ListView):
    model = models.Car      # shorthand for setting queryset = models.Car.objects.all()
    template_name = 'app/car_list.html'  # optional (the default is app_name/modelNameInLowerCase_list.html; which will look into your templates folder for that path and file)
    context_object_name = "car_list"    #default is object_list as well as model's_verbose_name_list and/or model's_verbose_name_plural_list, if defined in the model's inner Meta class
    paginate_by = 10  #and that's it !!
    
```

上面也可以通过 url 设置 `paginate_by` 属性：
```
url(r'^cars/$', ListView.as_view( model=Car, paginate_by=10 ))
```

template:
```html
{% if is_paginated %}
        <div class="pagination">
            <span class="page-links">
                {% if page_obj.has_previous %}
                    <a href="/cars?page={{ page_obj.previous_page_number }}">previous</a>
                {% endif %}
                <span class="page-current">
                    Page {{ page_obj.number }} of {{ page_obj.paginator.num_pages }}.
                </span>
                {% if page_obj.has_next %}
                    <a href="/cars?page={{ page_obj.next_page_number }}">next</a>
                {% endif %}
            </span>
        </div>
{% endif %}
```