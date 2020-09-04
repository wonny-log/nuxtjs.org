---
title: Runtime Config
description: RuntimeConfig를 통해 동적 구성 및 환경 변수를 nuxt context에 전달할 수 있습니다.
category: getting-started
position: 111
---

Nuxt.js는 `process.env`를 통해 구성을 제공하기 위해 [env](/api/configuration-env) 구성을 지원합니다. 웹팩의 [DefinePlugin](https://webpack.js.org/plugins/define-plugin/)에 의해 수행됩니다.

이 방식에는 두 가지 단점이 있습니다:

- 값이 빌드 타임에 읽혀지고 웹팩 번들에 유지됩니다. `process.env`를 변경하려면 [12factor](https://12factor.net/) 앱 디자인에 위배되는 리빌드를 해야 합니다.
- 클라이언트 사이드 번들에 시크릿 키를 노출하기 쉬워집니다.

왜 우리가 @nuxtjs/dotenv에서 런타임 컨피그로 옮겼는지에 대해 더 알아보고 싶다면 [이 글](/blog/moving-from-nuxtjs-dotenv-to-runtime-config)을 읽으세요.

### Runtime Config (2.13+)

`nuxt.config`에 두 가지 새로운 옵션이 추가 되어 context `$config`를 사용하여 액세스할 수 있는 런타임 구성을 전달할 수 있습니다.

구성은 Nuxt 페이로드(`__NUXT__.config`)에 추가되어 런타임 구성을 업데이트하기 위해 리빌드할 필요가 없습니다. SSR, SPA와 Static 타겟이 지원되지만 static 타겟의 경우에는 다시 generate하는 것이 필요합니다.

```js
export default {
  publicRuntimeConfig: {},
  privateRuntimeConfig: {}
}
```

- `publicRuntimeConfig` 는 `$config`를 통해 서버 사이드와 클라이언트 사이드 모두에서 이용할 수 있습니다.
- `privateRuntimeConfig` 는 동일한 `$config`를 통해 서버 사이드에서만 이용할 수 있습니다. (이는 `publicRuntimeConfig`를 오버라이드 합니다)

### 사용 방법

`$config` 는 context로부터 어디서든 이용할 수 있습니다. (페이지, 스토어, 플러그인 포함)

```js
export default {
  asyncData({ $config: { baseURL } }) {
    fetch(`${baseURL}/test`)
  },
  mounted() {
    console.log(this.$config.testValue)
  }
}
```

### `.env` 지원

프로젝트 루트 디렉터리에 `.env` 파일이 있다면 [dotenv](https://github.com/motdotla/dotenv)를 사용하여 자동으로 불러와지고 `process.env`를 통해 접근할 수 있습니다.

`process.env`가 업데이트 되어 런타임 구성을 위해 `nuxt.config` 내에서 바로 사용할 수 있습니다. 값들은 [dotenv-expand](https://github.com/motdotla/dotenv-expand)의 개선된 버전을 사용하여 보간되고 확장됩니다.

`.env` 파일도 `nuxt dev` 동안 리로드 되는 것을 관찰합니다. `--dotenv <file>`을 사용하거나 `--dotenv false`로 비활성화하여 env 경로를 맞춤 설정할 수 있습니다.

### 확장/보간 지원

> dotenv과 런타임 컨피그 모두 지원됩니다.

런타임 컨피그를 위한 확장은 이미 키가 있는 경우에만 발생합니다. (예시의 `API_SECRET`를 확인해주세요)

보간을 사용하면 env 변수를 쉽게 중첩할 수 있습니다. (예시의 `baseURL`를 확인해주세요)

`.env`:

```config
BASE_URL=/api
PUBLIC_URL=https://nuxtjs.org
API_SECRET=1234
```

`nuxt.config.js`:

```js
export default {
  publicRuntimeConfig: {
    baseURL: process.env.BASE_URL
  },
  privateRuntimeConfig: {
    baseURL: '${PUBLIC_URL}${BASE_URL}',
    API_SECRET: '${API_SECRET}' // process.env.API_SECRET를 사용하는 것과 비슷하다
  }
}
```

`publicRuntimeConfig` 및 `privateRuntimeConfig`에 대한 함수를 사용할 수 있지만 권장하지 않습니다.
