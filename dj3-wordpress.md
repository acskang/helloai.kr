## Integration django with wordpress
- django 프로젝트 생성: "letsrun"
- MariaDB 접속 정보 설정
  - settings.py
    DATABASES = {
        'default': {
            'ENGINE': 'django.db.backends.mysql',
            'NAME': 'wordpress',
            'USER': 'letsrun',
            'PASSWORD': 'minigate',
            'HOST': '127.0.0.1',
            'PORT': '3306',
            'OPTIONS': {
                'init_command': "SET sql_mode='STRICT_TRANS_TABLES'"
            },
        }
- "mysqlclient" 설치
  - $ pip install mysqlclient
- django 어플리케이션 생성: "wordpress"
- wordpress router 정보 설정
  - settings.py
      DATABASE_ROUTERS = ['wordpress.routers.DBAppsRouter']
      DATABASE_APPS_MAPPING = {
        'contenttype': 'default',
        'auth': 'default',
        'admin': 'default',
        'sessions': 'default',
        'messages': 'default',
        'staticfiles': 'default',
        'wordpress': 'default',
        }
  - wordpress/routers.py
      class DBAppsRouter(object):

        def db_for_read(self, model, **hints):
            if model._meta.app_label == 'wordpress':
                return 'default'
            return None

        def db_for_write(self, model, **hints):
            if model._meta.app_label == 'wordpress':
                return 'default'
            return None

        def allow_relation(self, obj1, obj2, **hints):
            if obj1._meta.app_label == 'wordpress' or \
               obj2._meta.app_label == 'wordpress':
                return True
            return None

        def allow_migration(self, db, app_label, model_name=None, **hints):
            if app_label == 'wordpress':
                return db == 'default'
            return None

- wordpress 모델 생성
  - $ python manage.py inspectdb --database=default > wordpress/models.py
      L default = settings.py/DATABASES

- django database migration

- django admin 에 wordpress 모델 등록
  - wordpress/admin.py
    from django.contrib import admin
    from django.contrib.admin.templatetags.admin_list import date_hierarchy
    from .models import WpComments

    # Register your models here.

    class AdminWpComments(admin.ModelAdmin):
      
     model = WpComments

        fieldsets = [
            (None, {'fields': ['comment_post_id']}),
            (None, {'fields': ['comment_author']}),
            (None, {'fields': ['comment_author_ip']}),
            (None, {'fields': ['comment_date']}),
            (None, {'fields': ['comment_date_gmt']}),
            (None, {'fields': ['comment_content']}),
            (None, {'fields': ['comment_karma']}),
            (None, {'fields': ['comment_approved']}),
            (None, {'fields': ['comment_agent']}),
            (None, {'fields': ['comment_parent']}),
            (None, {'fields': ['user_id']}),
        ]
        list_display = (
            'comment_id',
            'comment_post_id',
            'comment_author',
            'comment_date',
            'comment_date_gmt',
            'comment_karma',
            'comment_approved',
            'comment_parent',
            'user_id'
        )
        search_fields = ['comment_content']
        ordering = ('-comment_id',)

    admin.site.register(WpComments, AdminWpComments)

- wordpress REST API URL
  . http://url/wp-json/wp/v2/
  . http://url/wp-json/wp/v2/posts  # 포스트 데이터
  . http://url/wp-json/wp/v2/media  # 미디어 데이터 전체
  . http://url/wp-json/wp/v2/media/'mediaID'      # 미디어 데이터
  . http://url/wp-json/wp/v2/posts/?search=주차장  # 포스트 데이터 중 검색
    * options: search, author, page, per_page, offset, link, order(asc, desc), order by
    * fields: url/?_fields = 필드1, 필드2, 필드3 ...
        L http://192.168.5.15/wp-json/wp/v2/posts/?_fields=id,date,modified,title,content,categories,tags,_links
        L http://192.168.5.15/wp-json/wp/v2/posts_fields=id,date,modified,title,content,categories,tags,_links


### https://antilibrary.org/1904

# Django DRF-YASG (Yet another swagger generator)
$ conda activate django
$ pip install drf-yasg
-* settings.INSTALLED_APPS 등록 *-
    'drf_yasg'
-* Swagger End Point 추가 *-

# API Relational Modelds
https://kimdoky.github.io/django/2018/07/13/drf-Serializerrelations/

# django-phone-field
$ pip install django-phone-field
from phone_field import PhoneField, PhoneNumber

# Django-rest-auth 회원가입, 로그인, 로그아웃
$ pip install django-rest-auth
$ pip install django-allauth
https://freekim.tistory.com/8

