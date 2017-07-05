---
title: Django Views_functions
date: 2016-10-05 13:18:12
tags: [Django, Views]
category: Python

---


# A simple Views

    from django.http import HttpResponse
    import datetime

    def current_datetime(request):
        now = datetime.datetime.now()
        html = "<html><body>It is now %s.</body></html>" % now
        return HttpResponse(html)


添加后在urls中定义urlpatterns，重启uwsgi

<!--more-->

# Returning errors
。。。

# Customizing error views

Simply specify the handlers as seen below in your URLconf (setting them anywhere else will have no effect).

The page_not_found() view is overridden by handler404:

    handler404 = 'mysite.views.my_custom_page_not_found_view'
The server_error() view is overridden by handler500:

    handler500 = 'mysite.views.my_custom_error_view'
The permission_denied() view is overridden by handler403:

    handler403 = 'mysite.views.my_custom_permission_denied_view'
The bad_request() view is overridden by handler400:

    handler400 = 'mysite.views.my_custom_bad_request_view'
