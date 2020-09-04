---
title: 'RuntimeConfig 속성'
description: RuntimeConfig를 통해 동적 구성 및 환경 변수를 nuxt context에 전달할 수 있습니다.
menu: runtimeConfig
category: configuration-glossary
position: 25
---

RuntimeConfig를 통해 동적 구성 및 환경 변수를 nuxt context에 전달할 수 있습니다. 사용 방법에 대한 자세한 내용은 [runtime config guide](/guide/runtime-config)를 살펴보세요.

## `publicRuntimeConfig`

- Type: `Object`

이 객체의 값은 `$config`를 사용하여 클라이언트와 서버 사이드 모두에서 접근할 수 있습니다.

## `privateRuntimeConfig`

- Type: `Object`

이 객체의 값은 `$config`를 사용하여 서버 사이드에서만 접근할 수 있습니다. 서버 사이드에서는 `publicRuntimeConfig`를 오버라이드합니다.
