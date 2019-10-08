---
title: Django Views Built-in Views
date: 2016-10-03 13:18:12
tags: [Django, Views]
category: Python

---

# Serving files in development

`static.serve(request, path, document_root, show_indexes=False)`

用于测试环境

    from django.conf import settings
    from django.views.static import serve

    # ... the rest of your URLconf goes here ...

    if settings.DEBUG:
        urlpatterns += [
            url(r'^media/(?P<path>.*)$', serve, {
                'document_root': settings.MEDIA_ROOT,
            }),
        ]

# Error views

## The 404 (page not found) view¶

`defaults.page_not_found(request, exception, template_name='404.html')`

## The 500 (server error) view

`defaults.server_error(request, template_name='500.html')`

## The 403 (HTTP Forbidden) view

## The 400 (bad request) view
