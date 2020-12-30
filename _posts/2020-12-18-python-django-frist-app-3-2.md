---
layout: post
title: 첫 번째 장고 앱 작성하기, part 3-2
tags:
  - macos
  - python
  - django
  - sqlite
---

[[참고 URL](https://docs.djangoproject.com/ko/3.1/intro/tutorial03/#write-views-that-actually-do-something)]

### 뷰가 실제로 뭔가를 하도록 만들기

각 뷰는 두 가지 중 하나를 하도록 되어 있습니다. 요청된 페이지의 내용이 담긴 [HttpResponse](https://docs.djangoproject.com/ko/3.1/ref/request-response/#django.http.HttpResponse) 객체를 반환하거나, 혹은 [Http404](https://docs.djangoproject.com/ko/3.1/topics/http/views/#django.http.Http404) 같은 예외를 발생하게 해야합니다. 나머지는 당신에게 달렸습니다.

당신이 작성한 뷰는 데이터베이스의 레코드를 읽을 수도 있습니다. 또한 뷰는 Django나 Python에서 서드파티로 제공되는 템플릿 시스템을 사용할 수도 있습니다. 뷰는 PDF를 생성하거나, XML을 출력하거나, 실시간으로 ZIP 파일을 만들 수 있습니다. 뷰는 당신이 원하는 무엇이든, Python의 어떤 라이브러리라도 사용할 수 있습니다.

Django에 필요한 것은 [HttpResponse](https://docs.djangoproject.com/ko/3.1/ref/request-response/#django.http.HttpResponse) 객체 혹은 예외입니다.

왜냐면, 그렇게 다루는게 편리하기 때문입니다. [튜토리얼 2장](https://docs.djangoproject.com/ko/3.1/intro/tutorial02/)의 예제에서 다룬 Django 자체 데이터베이스 API를 사용해봅시다. 새로운 **index()** 뷰 하나를 호출했을 때, 시스템에 저장된 최소한 5 개의 투표 질문이 콤마로 분리되어, 발행일에 따라 출력됩니다.

`polls/views.py`
```py
from django.http import HttpResponse

from .models import Question


def index(request):
    latest_question_list = Question.objects.order_by('-pub_date')[:5]
    output = ', '.join([q.question_text for q in latest_question_list])
    return HttpResponse(output)

# Leave the rest of the views (detail, results, vote) unchanged
```

여기 몇가지 문제가 있습니다. 뷰에서 페이지의 디자인이 하드코딩 되어 있다고 합시다. 만약 페이지가 보여지는 방식을 바꾸고 싶다면, 이 Python 코드를 편집해야만 할 겁니다. 그럼, 뷰에서 사용할 수 있는 템플릿을 작성하여, Python 코드로부터 디자인을 분리하도록 Django의 템플릿 시스템을 사용해 봅시다.

우선, **polls**디렉토리에 **templates**라는 디렉토리를 만듭니다. Django는 여기서 템플릿을 찾게 될 것입니다.

프로젝트의 [TEMPLATES](https://docs.djangoproject.com/ko/3.1/ref/settings/#std:setting-TEMPLATES) 설정은 Django가 어떻게 템플릿을 불러오고 렌더링 할 것인지 기술합니다. 기본 설정 파일은 [APP_DIRS](https://docs.djangoproject.com/ko/3.1/ref/settings/#std:setting-TEMPLATES-APP_DIRS) 옵션이 **True**로 설정된 **DjangoTemplates** 백엔드를 구성합니다. 관례에 따라, **DjangoTemplates**은 각 [INSTALLED_APPS](https://docs.djangoproject.com/ko/3.1/ref/settings/#std:setting-INSTALLED_APPS) 디렉토리의 《templates》 하위 디렉토리를 탐색합니다.

방금 만든 **templates** 디렉토리 내에 **polls**라는 다른 디렉토리를 만들고 그 안에 **index.html**이라는 파일을 만듭니다. 즉, 템플릿은 **polls/templates/polls/index.html**에 있어야합니다. 위에서 설명한대로 **app_directories** 템플릿 로더가 작동하는 방식 때문에 Django 내에서이 템플릿을 **polls/index.html**로 참조 할 수 있습니다.


템플릿에 다음과 같은 코드를 입력합니다.

`polls/templates/polls/index.html`

(체크) \\&#37; 에서 \\를 제거하여 소스에 사용해야 합니다.


    {\% if latest_question_list %}
    <ul>
    {\% for question in latest_question_list %}
        <li><a href="/polls/{{ question.id }}/">{{ question.question_text }}</a></li>
    {\% endfor %}
    </ul>
    {\% else %}
        <p>No polls are available.</p>
    {\% endif %}

``` html
<html lang="ko">
<head>
    <title>{{ site.name }}</title>

    {% block css %}
    {% endblock %}
</head>
<body class="st-container st-effect-3">

<div class="wrapper">

    {% block leader %}
        <h1>{% block title %}Title{% endblock %}</h1>
        <p>{% block subtitle %}Subtitle{% endblock %}</p>
    {% endblock %}

    {% block content %}
    {% endblock %}
</div>
</body>
</html>
```

이제, 템플릿을 이용하여 polls/views.py에 index 뷰를 업데이트 해보도록 하겠습니다.

`polls/views.py`
```python
from django.http import HttpResponse
from django.template import loader

from .models import Question


def index(request):
    latest_question_list = Question.objects.order_by('-pub_date')[:5]
    template = loader.get_template('polls/index.html')
    context = {
        'latest_question_list': latest_question_list,
    }
    return HttpResponse(template.render(context, request))
```

이 코드는 **polls/index.html** 템플릿을 불러온 후, context를 전달합니다. context는 템플릿에서 쓰이는 변수명과 Python 객체를 연결하는 사전형 값입니다.

브라우저에서 《/polls/》 페이지를 불러오면, [튜토리얼 2장](https://docs.djangoproject.com/ko/3.1/intro/tutorial02/)에서 작성한 《What’s up》 질문이 포함된 리스트가 표시됩니다. 표시된 질문의 링크는 해당 질문에 대한 세부 페이지를 가리킵니다.

![polls-index](/images/202012/2020-12-18-python-frist-app-3-2.png "polls-index")

#### 지름길: [render()](https://docs.djangoproject.com/ko/3.1/topics/http/shortcuts/#django.shortcuts.render)

템플릿에 context 를 채워넣어 표현한 결과를 [HttpResponse](https://docs.djangoproject.com/ko/3.1/ref/request-response/#django.http.HttpResponse) 객체와 함께 돌려주는 구문은 자주 쓰는 용법입니다. 따라서 Django는 이런 표현을 쉽게 표현할 수 있도록 단축 기능(shortcuts)을 제공합니다. **index()** 뷰를 단축 기능으로 작성하면 다음과 같습니다.

`polls/views.py`
```py
from django.shortcuts import render

from .models import Question


def index(request):
    latest_question_list = Question.objects.order_by('-pub_date')[:5]
    context = {'latest_question_list': latest_question_list}
    return render(request, 'polls/index.html', context)
```

모든 뷰에 적용한다면, 더 이상 [loader](https://docs.djangoproject.com/ko/3.1/topics/templates/#module-django.template.loader)와 [HttpResponse](https://docs.djangoproject.com/ko/3.1/ref/request-response/#django.http.HttpResponse)를 임포트하지 않아도 됩니다. (만약 **detail**, **results**, **vote**에서 stub 메소드를 가지고 있다면, **HttpResponse**를 유지해야 할 것입니다.)

[render()](https://docs.djangoproject.com/ko/3.1/topics/http/shortcuts/#django.shortcuts.render) 함수는 request 객체를 첫번째 인수로 받고, 템플릿 이름을 두번째 인수로 받으며, context 사전형 객체를 세전째 선택적(optional) 인수로 받습니다. 인수로 지정된 context로 표현된 템플릿의 [HttpResponse](https://docs.djangoproject.com/ko/3.1/ref/request-response/#django.http.HttpResponse) 객체가 반환됩니다.


---

### 404 에러 일으키기

이제, 질문의 상세 뷰에 태클을 걸어보겠습니다. 상세 뷰는 지정된 설문조사의 질문 내용을 보여줍니다. 다음과 같습니다.

`polls/views.py`
```py
from django.http import Http404
from django.shortcuts import render

from .models import Question
# ...
def detail(request, question_id):
    try:
        question = Question.objects.get(pk=question_id)
    except Question.DoesNotExist:
        raise Http404("Question does not exist")
    return render(request, 'polls/detail.html', {'question': question})
```

여기 새로운 내용이 추가되었습니다. 뷰는 요청된 질문의 ID 가 없을 경우 [Http404](https://docs.djangoproject.com/ko/3.1/topics/http/views/#django.http.Http404) 예외를 발생시킵니다.

조금 후에 **polls/detail.html** 템플릿에 무엇을 넣을 수 있는지 논의하겠지만, 일단 위의 예제를 동작시키기 위해 아래의 내용이 들어있는 파일을 작성하세요.

`polls/templates/polls/detail.html`

```
    {{ question }}
```

이제 시작해도 됩니다.


### 지름길: [get_object_or_404()](https://docs.djangoproject.com/ko/3.1/topics/http/shortcuts/#django.shortcuts.get_object_or_404)

만약 객체가 존재하지 않을 때 [get()](https://docs.djangoproject.com/ko/3.1/ref/models/querysets/#django.db.models.query.QuerySet.get) 을 사용하여 Http404 예외를 발생시키는것은 자주 쓰이는 용법입니다. Django에서 이 기능에 대한 단축 기능을 제공합니다. **detail()** 뷰를 단축 기능으로 작성하면 다음과 같습니다.

`polls/views.py`
```py
from django.shortcuts import get_object_or_404, render

from .models import Question
# ...
def detail(request, question_id):
    question = get_object_or_404(Question, pk=question_id)
    return render(request, 'polls/detail.html', {'question': question})
```

[get_object_or_404()](https://docs.djangoproject.com/ko/3.1/topics/http/shortcuts/#django.shortcuts.get_object_or_404) 함수는 Django 모델을 첫번째 인자로 받고, 몇개의 키워드 인수를 모델 관리자의 [get()](https://docs.djangoproject.com/ko/3.1/ref/models/querysets/#django.db.models.query.QuerySet.get) 함수에 넘깁니다. 만약 객체가 존재하지 않을 경우, [Http404](https://docs.djangoproject.com/ko/3.1/topics/http/views/#django.http.Http404) 예외가 발생합니다.


철학
> 상위 계층에서 [ObjectDoesNotExist](https://docs.djangoproject.com/ko/3.1/ref/exceptions/#django.core.exceptions.ObjectDoesNotExist) 예외를 자동으로 잡아 내는 대신 [get_object_or_404()](https://docs.djangoproject.com/ko/3.1/topics/http/shortcuts/#django.shortcuts.get_object_or_404) 도움 함수(helper functoin)를 사용하거나, [ObjectDoesNotExist](https://docs.djangoproject.com/ko/3.1/ref/exceptions/#django.core.exceptions.ObjectDoesNotExist) 예외를 사용하는 대신 [Http404](https://docs.djangoproject.com/ko/3.1/topics/http/views/#django.http.Http404) 를 사용하는 이유는 무엇일까요?
>
> 왜냐하면, 모델 계층을 뷰 계층에 연결하는 방법이기 때문입니다. Django의 중요한 설계 목표는, 약결합(loose coupling)을 관리하는 데에 있습니다. 일부 제어된 결합이 [django.shortcuts](https://docs.djangoproject.com/ko/3.1/topics/http/shortcuts/#module-django.shortcuts) 모듈에서 도입되었습니다.

또한, [get_object_or_404()](https://docs.djangoproject.com/ko/3.1/topics/http/shortcuts/#django.shortcuts.get_object_or_404) 함수처럼 동작하는 [get_list_or_404()](https://docs.djangoproject.com/ko/3.1/topics/http/shortcuts/#django.shortcuts.get_list_or_404) 함수가 있습니다. [get()](https://docs.djangoproject.com/ko/3.1/ref/models/querysets/#django.db.models.query.QuerySet.get) 대신 [filter()](https://docs.djangoproject.com/ko/3.1/ref/models/querysets/#django.db.models.query.QuerySet.filter) 를 쓴다는 것이 다릅니다. 리스트가 비어있을 경우, [Http404](https://docs.djangoproject.com/ko/3.1/topics/http/views/#django.http.Http404) 예외를 발생시킵니다.

![polls-404](/images/202012/2020-12-18-python-frist-app-3-2-1.png "polls-404")

