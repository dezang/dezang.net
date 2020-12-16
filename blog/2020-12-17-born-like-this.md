---
slug: blog-on-docusaurus
title: 도큐사우루스로 블로그 시작
tags: [docusaurus]
---

## 설치하기
https://v2.docusaurus.io/docs/installation/

```sh
npx @docusaurus/init@latest init hwangdezang.com classic

cd hwangdezang.com
yarn start
```

## Build

```console
yarn build
```

This command generates static content into the `build` directory and can be served using any static contents hosting service.

## Deployment

```console
GIT_USER=<Your GitHub username> USE_SSH=true yarn deploy
```

If you are using GitHub pages for hosting, this command is a convenient way to build the website and push to the `gh-pages` branch.
