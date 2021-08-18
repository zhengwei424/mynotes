#### ASGI 不能正常加载static目录文件，WSGI能正常加载

```bash
[root@localhost monitor]# tree -L 2
.
├── apps
│   ├── cluster
│   ├── config
│   ├── __init__.py
│   ├── k8s
│   ├── service
│   └── workload
├── envs.py
├── k8s_resources
│   ├── auth.py
│   ├── deploy.py
│   ├── endpoint.py
│   ├── namespace.py
│   ├── node.py
│   ├── pod.py
│   ├── __pycache__
│   └── service.py
├── manage.py
├── monitor
│   ├── asgi.py
│   ├── __init__.py
│   ├── routing.py
│   ├── settings.py  # 修改
│   ├── urls.py      # 修改
│   └── wsgi.py
├── Pipfile
├── Pipfile.lock
├── __pycache__
│   ├── envs.cpython-38.pyc
│   ├── global_values.cpython-38.pyc
│   └── tools.cpython-38.pyc
├── requirements.txt
├── static
│   ├── ace
│   ├── admin
│   ├── layui-v2.6.7
│   ├── mycss
│   ├── myjs
│   └── node_modules
├── templates
│   ├── ace_editor.html
│   ├── base.html
│   ├── cluster
│   ├── pod_log.html
│   ├── terminal.html
│   ├── test.html
│   └── workload
├── test.py
└── tools.py
```
##### 修改settings.py
```bash
settings.py:
#
# 指定ASGI的路由地址(webSocket)
ASGI_APPLICATION = 'monitor.asgi.application'
#
# 增加STATIC_ROOT
STATIC_ROOT = os.path.join(BASE_DIR, 'static')
#
STATIC_URL = '/static/'
STATICFILES_DIRS = [
    # 在访问http://host:port/static/时，会在该目录下查找，目录名可以为任意
    os.path.join(BASE_DIR, 'static'),
    # os.path.join(BASE_DIR, 'public')
]
#
# 可忽略
STATICFILES_FINDERS = (
    'django.contrib.staticfiles.finders.FileSystemFinder',
    'django.contrib.staticfiles.finders.AppDirectoriesFinder',
)
```

##### 修改urls.py
```bash
from django.contrib import admin
from django.urls import path, include
# 增加
from django.conf import settings
from django.conf.urls.static import static
# 增加 + static(settings.STATIC_URL, document_root=settings.STATIC_ROOT)
urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include(('apps.k8s.urls', 'k8s'), namespace='k8s')),
    path('cluster/', include(('apps.cluster.urls', 'cluster'), namespace='cluster')),
    # path('workload/', include(('apps.workload.urls', 'workload'), namespace='workload')),
    # path('service/', include(('apps.service.urls', 'service'), namespace='service')),
    # path('config/', include(('apps.config.urls', 'config'), namespace='config')),
] + static(settings.STATIC_URL, document_root=settings.STATIC_ROOT)
```
