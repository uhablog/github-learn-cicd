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

### ステータスチェック関数

GitHub Actionsではステータスチェック関数が用意されており、手前で実行したステップやジョブの結果によって返す値が変化する。

- success(): 手前の処理が成功したらtrue
- failure(): 手前の処理が失敗したらtrue
- cancelled(): 手前の処理がキャンセルされたらtrue
- always(): 手前の処理結果を問わず常にtrue

failureなどは失敗した時に通知するといった処理でよく使われる。

### ステップ間のデータ共有

GITHUB_OUTPUT環境変数を利用することで、ステップ間でデータを共有できる

```
```
