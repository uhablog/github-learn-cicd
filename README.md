# github-learn-cicd

## ch03

コンテキストを直接シェルコマンドへ埋め込むのはアンチパターン。
以下のように中間環境変数を利用する

```
name: Intermediate environment variables
on: push
jobs:
  print:
    runs-on: ubuntu-latest
    env:
      ACTOR: ${{ github.actor }}
    steps:
      - run: echo "${ACTOR}"
```
