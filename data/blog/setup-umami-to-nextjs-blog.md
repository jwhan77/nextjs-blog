---
title: Next.js 프로젝트에 Umami analytics 설정하기
date: '2022-05-15'
tags: ['nextjs', 'umami', 'analytics']
draft: false
summary: Next.js 블로그 방문자 수를 카운트하기 위해 analytics를 설정해보자
---

이 블로그는 [오픈소스 Next.js 블로그 템플릿](https://github.com/timlrx/tailwind-nextjs-starter-blog)을 이용해 만들어졌다.

해당 템플릿에서는 여러 애널리틱스 솔루션을 지원하는데, plausible, simpleAnalytics, umami, google analytics 를 지원한다.

내가 원하는 조건은 아래와 같았고 네 가지 옵션을 모두 확인한 결과 umami가 가장 마음에 들었다.

1. 무료일 것
2. 사용하기 간단할 것
3. 블로그와 각 포스팅 방문자 수 확인

Umami 애널리틱스는 해당 블로그 템플릿이 아니더라도 **Next.js를 사용한 프로젝트면 모두 사용가능**하니 도움이 되었으면 한다.

---

# Umami 란?

[Umami analytics 공식 홈페이지](https://umami.is/)

공식 홈페이지에서는 umami를 간단하고, 사용하기 쉽고, self-hosted 웹 애널리틱스 솔루션이라고 소개하고 있다.

공식 문서가 굉장히 깔끔하게 정리되어 있어 사용법을 이해하기도 쉽다.

사용 방법은 localhost에서 실행하는 방법과 웹 호스팅을 하는 방법이 있는 것 같은데, 나는 그냥 호스팅하기로 결정했다.

<br />

## Heroku 사용해서 umami 호스팅하기

호스팅에도 여러 옵션이 있다. DigitalOcean, Vercel, Netlify, Heroku 등.

처음에는 Vercel로 호스팅하려고 했는데, 결론적으로 Heroku를 사용했다.

왜냐하면 웹 호스팅을 하기 위해서는 클라우드 데이터베이스를 사용해야하는데, 내가 클라우드 데이터베이스 솔루션으로 Heroku Postgres를 선택했기 때문이다.

우선 [공식 문서 - Running on Heroku](https://umami.is/docs/running-on-heroku) 에 나온 것 처럼 버튼 하나로 자동 deploy를 해도 되지만, 여러 번의 삽질 끝에 문제가 있어 나는 heroku app을 만드는 것부터 시작했다.

<br />

### Create app

Heroku 계정을 만든 다음 가장 먼저 app을 만들어줘야한다.

계정을 만들고 dashboard로 가면 Create App 버튼이 있다.

앱 이름은 아무거나 상관없다.

<br />

### Install Heroku Postgres

앱을 만들고 나면 앱 대시보드로 들어간 후 Resources를 클릭한다. 혹은 [여기](https://elements.heroku.com/addons/heroku-postgresql)로 바로 접속해도 된다.

나는 Hobby Dev 플랜을 사용했고 'Install Heroku Postgres' 버튼을 클릭한다.

그러면 Add-on plan이 Hobby Dev - Free가 맞는지 다시 한 번 더 확인하고, App to provision to 에서 앞서 생성했던 app을 검색해서 연결해준다.

<br />

### Deploy Umami app

이제 Umami app을 deploy 해야한다.

패널에 들어가면 Deploy 탭이 있고 Deployment method에 GitHub이 있지만 현재 보안 문제로 Github과 연동되지 않는다.

그래서 나는 [umami github 레파지토리](https://github.com/mikecao/umami) 를 포크한 후 git clone을 해줬다.

```powershell
git clone https://github.com/GITHUB_USERNAME/umami.git
cd umami
```

heroku 명령어를 사용하려면 Heroku CLI 설치가 필요하다. 설치와 관련된 자세한 내용은 [이 문서](https://devcenter.heroku.com/articles/heroku-cli#install-the-heroku-cli)를 참고.

heroku에 한 번도 로그인한 적 없다면 로그인을 먼저 해준다.

```powershell
heroku login
```

명령어를 실행하면 웹 페이지가 뜨고 Login 버튼을 눌러주면 된다.

로그인 후 다음 명령어를 실행해서 heroku 앱과 레파지토리를 연결해준다.

```powershell
heroku git:remote -a YOUR_HEROKU_APP_NAME
```

그런 다음 `git remote -v`를 실행하면 heroku 가 등록된 것을 확인할 수 있다.

이제 deploy만 남았다. 아래 명령어를 실행한다.

```powershell
git push heroku master
```

다시 heroku app 패널로 들어가서 Settings - Config Vars 를 수정해줘야한다.

umami를 사용하기 위해서는 두 가지 환경 변수가 필요한데, `DATABASE_URL` 과 `HASH_SALT` 이다.

`DATABASE_URL` 의 경우 이미 우리가 Heroku Postgre를 설치하는 과정에서 자동으로 등록되어 있음을 볼 수 있다.

따라서 `HASH_SALT`를 추가해주자. 값은 아무 string이면 상관없다.

<br />

### 데이터베이스 설정
<br />

![https://umami.is/login.png](https://umami.is/login.png)

이제 패널의 Open app 을 누르면 umami 페이지가 뜬다. 감격스러운 순간이다.

하지만 로그인을 하려는 순간 (초기 username과 password는 admin, umami다.) Internal Server Error가 뜨는 것을 볼 수 있다.

당연하다. 데이터베이스 설정을 안했기 때문이다.

이제 Heroku에 데이터베이스를 설정해보자.

이 부분에서 엄청 해맸는데 [이 포스팅](https://chenhuijing.com/blog/setting-up-umami-on-heroku/)을 보고 도움을 받았다.

공식 문서에 따르면 데이터베이스를 생성하기 위해서는 아래와 같이 명령어를 실행해줘야한다.

```powershell
psql -h hostname -U username -d databasename -f sql/schema.postgresql.sql
```

이걸 heroku 명령어를 사용해서 바로 데이터베이스를 생성할 수 있다.

아까 app 패널의 Settings - Config Vars에서 Reveal Config Vars를 클릭했을 때 `DATABASE_URL`이 자동으로 등록되어 있는 것을 볼 수 있었을 것이다. 그 정보 안에 username, password, host, port, dbname 정보가 들어있다.

```shell
postgres://username:password@host:port/dbname
```

이 정보를 재배합해서 아래의 명령어를 터미널에서 실행해준다.

```powershell
heroku run psql -h YOUR_HOST_IP.compute.amazonaws.com -U HEROKU_ASSIGNED_USERNAME -d HEROKU_ASSIGNED_DBNAME -f sql/schema.postgresql.sql -a APPLICATION_NAME
```

그러면 설치가 끝난다.

로그인도 제대로 동작하는 것을 볼 수 있다.

참고로 처음 로그인 후 비밀번호를 꼭 바꿀 것. umami login에 대한 내용은 [여기](https://umami.is/docs/login)에서 더 자세히.

<br />

## Umami에 웹사이트 추가하기

umami 자체의 settings에서 도메인을 추가하는 방법은 [여기](https://umami.is/docs/add-a-website)에 잘 설명되어있다.

문제는 이걸 이제 어떻게 Next.js 블로그에 추가할 거냐는거다.

공식 문서에 따르면 script를 `<head>`에 추가하라고 되어있다. 만약 본인이 나처럼 Next.js 블로그 템플릿을 사용하는게 아니라 Next.js 를 기반으로 한 웹사이트를 개발한거라면 `_app.ts`(혹은 `_app.js`) 에서 next/head를 사용해서 스크립트를 추가해주면 된다.

하지만 나는 블로그 템플릿을 사용하므로 템플릿의 룰에 맞춰서 설정해보겠다.

script 코드를 얻기 위해서는 umami 앱 - Settings - Websites 에서 아래 버튼을 클릭한다.

![https://umami.is/get-tracking-code.png](https://umami.is/get-tracking-code.png)

그러면 아래와 같은 script 코드가 뜬다.

```js
<script async defer data-website-id="abcd1234-ab12-cd34-1234-abcdef123456" src="https://YOUR_DOMAIN_NAME/umami.js"></script>
```

여기에서 data-website-id가 를 `siteMetadat.js`의 analytics - umamiWebsiteId에 추가한다.

```json
const siteMetadata = {
  ...
  "analytics": {
    ...
    "umamiWebsiteId": 'YOUR_WEBSITE_ID'
    ...
  },
  ...
}
...
```

다음으로 `next.config.js`의 CSP를 수정해준다.

```js
const ContentSecurityPolity = `
  ...
  script-src ... YOUR_UMAMI_APP_DOMAIN_NAME
  ...
`
...
```

저렇게 script-src의 마지막 부분에 heroku로 배포한 umami app 도메인 주소를 넣어주면 된다. (예: `{YOUR_HEROKU_APP_NAME}.herokuapp.com`) 

마지막으로 `components/analytics/Umami.tsx`에서 `src="https://umami.example.com/umami.js"` 부분을 위에서 얻은 script 코드의 src로 수정해준다.

이제 Next.js 블로그를 Vercel로 다시 deploy하면 제대로 동작하는 것을 확인할 수 있다.

![Umami Dashboard](/static/images/blog/umami-dashboard.jpg)


---

참고로 트래킹 방지 기능을 사용하는 유저의 기록은 업데이트 되지 않는다.

나는 Brave 브라우저를 사용하고 있는데, Brave는 자동으로 트래킹을 블록하므로 테스트 시 방문자 수 카운트가 안되는 것을 확인할 수 있었다. 하지만 크롬에서는 제대로 업데이트 되는 것을 확인했다.

다음에는 블로그에 댓글 기능을 추가해야겠다.


<br />
---

참고

- [umami 공식 문서](https://umami.is/docs/about)
- [Setting up umami on Heroku](https://chenhuijing.com/blog/setting-up-umami-on-heroku/)

모든 Umami 관련 이미지 출처는 [Umami 공식 홈페이지](https://umami.is)