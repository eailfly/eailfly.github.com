Title: 使用 Github Actions 部署 Pelican
Date: 2020-07-13 17:06

使用 Github 托管静态页面很方便，Pelican 也非常好用，但是每次发布都需要手动操作，从网上找到了使用`Github Actions` 自动发布页面的方法。此方法引用自[这里](https://nolanbconaway.github.io/blog/2020/deploying-my-pelican-website-to-github-pages.html)

## 创建 Personal Token
在[这里](https://github.com/settings/tokens)创建Personal Token.

然后在project的`Setting-Secrets`添加一个 Name 是`PERSONAL_TOKEN`，Value 是刚才生成的Token的 Secrets。

## 创建 Actions
在 `sources` 分支添加`.github/workflows/publish.yaml`
```yaml
name: Publish

on:
  push:
    branches:
      - sources

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v2
      - uses: actions/setup-python@v2
        with:
          python-version: "3.7"

      - name: Install Dependencies
        run: |
          python -m pip install --upgrade pip
          pip install -r requirements.txt
          git clone https://github.com/alexandrevicenzi/Flex.git
          pelican-themes -i Flex

      - name: Build Production Site
        run: |
          make publish
          cp README.md output/README.md
          touch output/.nojekyll

      - name: Deploy To Master Branch
        uses: peaceiris/actions-gh-pages@v3
        with:
          publish_branch: master
          publish_dir: ./output
          personal_token: ${{ secrets.PERSONAL_TOKEN }}
```