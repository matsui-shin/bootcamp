---
footer: CC BY-SA Licensed | Copyright (c) 2024, Internet Initiative Japan Inc.
title: Dockerを触ってみよう
description: Docker の概要を学び、コンテナ操作を体験します
time: 1h
prior_knowledge: 仮想化、CUI 操作、Linux基本操作（推奨）
updated: 2025-07
---

<header-table/>

# {{$page.frontmatter.title}}

## はじめに

Dockerは、当初 Docker Inc. によって開発され、現在は Mirantis を中心にコミュニティと共に保守・開発が続けられている、コンテナ型の仮想環境プラットフォームです。
コンテナとは、1台のコンピュータ上で複数のアプリケーションを分離して動かすための、軽量かつ高速な仮想化技術のひとつです。

Dockerを利用することで、アプリケーションとその依存関係を「コンテナ」と呼ばれる独立した実行環境としてパッケージ化し、どこでも同じように動作させることができます。

では、「コンテナ」と「仮想マシン」には、どのような違いがあるのでしょうか？

従来の仮想マシンでは、ホストOS上に仮想化ソフトウェア（例：VirtualBox、VMwareなど）を動かし、その中でゲストOSを実行します。
一方、コンテナはホストOSのカーネルを共有しつつ、独立したユーザー空間を構築することで、軽量で高速な実行環境を提供します。

### 本講義の目的

- Dockerについての基礎的な知識と仕組みを理解する
- Dockerfile および `docker` コマンドを使って、コンテナの作成・操作を体験する

#### 本講義で扱わないこと

- `docker` コマンドのすべてのオプション解説
- コンテナイメージのレイヤー構造などの詳細な内部仕様

### 本講義の目標

- `docker` コマンドを使って、コンテナの取得・起動・停止ができるようになる
- Dockerfileの基本的な書き方・読み方を理解し、自分の目的に応じてコンテナを構築できるようになる

### "Docker" とは

ここで扱う "Docker" とは、コンテナ仮想化プラットフォームおよびその周辺ツール群を指します。

以下はDockerを扱う上で基本となる概念です。

- **Docker コンテナ**
  アプリケーションとその実行環境をパッケージ化したもの。OSやハードウェアに依存せず、どこでも同じ動作を再現できます。

- **Docker イメージ**
  コンテナの元となるファイルシステムのテンプレート。アプリケーションとその依存ファイル、設定が含まれており、Dockerfileを用いて構築します。

- **コンテナオーケストレーション**
  複数のコンテナを効率よく展開・管理する仕組み。シンプルな構成管理には `docker compose` を、より大規模なシステムには `Kubernetes`（k8s）がよく利用されます。

  ※ 本講義では `docker compose`（Docker Compose v2 以降）を前提とします。従来の `docker-compose`（v1）は非推奨です。

- **イメージの共有と配布**
  Docker Hub や GitHub Container Registry などのリポジトリを通じて、コンテナイメージを共有・再利用できます。

- **OCI (Open Container Initiative)**
  現在、コンテナ技術は OCI により標準化されており、Dockerはその仕様に準拠した実装のひとつです。Podmanやcontainerdなども同様にOCI準拠です。

本講義では「Dockerコンテナ」と「Dockerイメージ」の操作を中心に学習を進めていきます。
「コンテナオーケストレーション」については、後続の `docker compose` の講義で触れます。

### 講義の進め方

この講義は、Dockerがあらかじめインストールされている環境を前提としています。
未インストールの場合は、事前準備ガイド「ハンズオン事前準備」に従ってセットアップを済ませてください。

> 💡 注意：Docker Desktop は商用利用に制限があります。Linux では `docker-ce` や `podman` などの代替も検討してください。

## Chapters

- [Dockerコンテナで仮想環境プラットフォームを構築する](./GETSTART.md)
- [Dockerコンテナイメージを作成して起動する](./RUN_AS_IMAGE.md)
- [Dockerコンテナの管理](./OPERATION.md)
- [Dockerイメージの作成](./BUILD.md)

## 参考

### 仮想マシン vs コンテナ

仮想マシンとコンテナ、どちらが「優れている」というよりは、それぞれ用途や目的に応じた使い分けが重要です。

コンテナは、仮想マシンよりも軽量かつ高速な環境構築が可能であり、CI/CDやマイクロサービスアーキテクチャとの相性が良いのが特徴です。
ただし、仮想マシンのように完全に分離された環境を必要とする場合は、VMの方が適していることもあります。

例）

- コンテナ：Linux 上で Linux アプリケーションを高速起動したい
- 仮想マシン：Linux 上で Windows アプリケーションを実行したい

### Docker のアーキテクチャ

![Docker Image](https://docs.docker.com/get-started/images/docker-architecture.webp)

### Docker Hubのレート制限について

Docker Hub には、匿名ユーザでのpull操作に対してレート制限（1時間に最大100回）が設けられています。大量にpullする場合は `docker login` を行うことで制限緩和されます。

### セキュリティの観点（発展）

実務では、以下のセキュリティ対策が重要です：

- コンテナイメージの脆弱性スキャン：`trivy`, `dockle`, `Grype` などのツール
- Dockerfileでの `USER` 指定（root実行の回避）

### 発展：DockerとWebAssembly（WASM）

近年は Docker が WebAssembly（WASM）との統合にも対応し始めており、軽量かつクロスプラットフォームな実行環境として注目されています。

<credit-footer/>