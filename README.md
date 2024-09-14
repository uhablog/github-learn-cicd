# github-learn-cicd

## ch02

### 手動実行

workflow_dispatchを指定することで、手動実行が可能となる

```manual.yml
name: manual
on:
  workflow_dispatch
jobs:
  manual:
    runs-on: ubuntu-latest
  steps:
    - run: echo Manual workflow!
```

## ch03

コンテキストを直接シェルコマンドへ埋め込むのはアンチパターン。
以下のように中間環境変数を利用する

```intermediate-environment-variables.yml
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

GITHUB_OUTPUT環境変数を利用することで、ステップ間でデータを共有できる。

```
name: GITHUB_OUTPUT
run-name: Run by @${{ github.actor }}
on: push
jobs:
  share:
    runs-on: ubuntu-latest
    steps:
      - id: source
        run: echo "result=Hello" >> "${GITHUB_OUTPUT}"
      - env:
          RESULT: ${{ steps.source.outputs.result }}
        run: echo "${RESULT}"
```

## ch04

### タイムアウト

timeout-minitesを使うことで、分単位でタイムアウトを指定できる。ジョブレベル・ステップレベルどちらでも指定が可能。

デフォルトタイムアウトは360分と非常に長い時間が設定されている。そのため基本的には全てのワークフローにタイムアウト時間を設定するべき。

```timeout.yaml
name: Timeout
on: push
jobs:
  sleep:
    runs-on: ubuntu-latest
    timeout-minites: 1
    steps:
      - run: sleep 20
```

## Ch05

### 複数ジョブの実行制御

**並列実行**

ジョブは基本的に並列で実行できる。ジョブを分割することで実行時間を短縮する事ができる。

```parallel-jobs.yml
name: Parallel jobs
on: push
jobs:
  first:
    runs-on: ubuntu-latest
    steps:
      - run: sleep 30 && echo "First job"
  second:
    runs-on: ubuntu-latest
    steps:
      - run: sleep 30 && echo "Second job"
  third:
    runs-on: ubuntu-latest
    steps:
      - run: sleep 30 && echo "Third job"
```

**逐次実行**

依存関係がある場合などは逐次実行することもできる。逐次実行するためにはneedsを指定する。

```sequential-jobs.yml
name: Sequential jobs
on: push
jobs:
  first:
    runs-on: ubuntu-latest
    steps:
      - run: sleep 10 && echo "First job"
  second:
    runs-on: ubuntu-latest
    needs: [first]
    steps:
      - run: sleep 10 && echo "second job"
  third:
    runs-on: ubuntu-latest
    needs: [second]
    steps:
      - run: sleep 10 && echo "Third job"
```

### Environments

テスト環境と本番環境などがある場合、Environmentsを利用することで、環境を分ける事ができる。
