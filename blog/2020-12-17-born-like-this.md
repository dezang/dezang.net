---
slug: blog-on-docusaurus
title: 도큐사우루스로 블로그 시작
tags: [docusaurus]
---

## 설치하기
https://v2.docusaurus.io/docs/installation/

```sh
npx @docusaurus/init@latest init dezang.net classic

cd dezang.net
yarn start
```

## 빌드하기

```console
yarn build
```

> This command generates static content into the `build` directory and can be served using any static contents hosting service.

## 배포하기

도큐사우루스는 아래와 같이 배포를 가이드합니다. 

```console
GIT_USER=<Your GitHub username> USE_SSH=true yarn deploy
```

처음에는 자연스럽게 github page 를 사용하려고 했지만, 좀 더 성능이 좋은 (블로그에 무슨 성능 타령인지... 직업병이란...) 서비스가 없을까 찾아보던 중 비교 정리를 잘해놓은 [nextJS 뭘로 배포할까? (Netlify, Vercel, Github page)](https://taeny.dev/javascript/nextjs-with-deployment-platform/) 을 보고 결정했습니다. 해당 글에 달린 댓글이 결정에 중요한 요소가 되었는데요.

> 깃허브 Netlify에 비해 Vercel의 특장점은 유일하게 서울에 CDN이 있다는 것입니다. Vercel의 경우 국내에서 TTFB가 30~40ms 로 최상급서버의 성능을 느낄 수 있습니다!

실제로 vercel에 경우 [Vercel Docs-Edge Network-Regions](https://vercel.com/docs/edge-network/regions) 페이지를 보면 _Seoul, South Korea_ 가 당당히 자리잡고 있는 것을 확인하였습니다. 반면 Netlify 는 [Netlify for Business](https://www.netlify.com/enterprise/) 플랜에만 _27 global edge locations_ 라고 명시해놓았을 뿐 프리 버전에 엣지가 어디에 몇 개인지는 비공개로 해놓았죠. 한글로 된 글이 대다수인 해당 블로그는 한국 엣지를 가지고 있는 것이 성능상 이점이 높을 것이라고 생각하여 이전에 써본 적은 없지만 vercel에 배포하기로 결정했습니다.

깃허브 로그인 후 몇 가지 설정만하면 바로 배포가 되었고, 커스텀 도메인도 아주 쉽고 빠르게 적용할 수 있었습니다. 무엇보다 _Let's Encrypt_ 에서 SSL 인증서를 자동으로 발급받아서 적용해주는 것도 좋았네요.
