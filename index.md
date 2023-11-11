---
title: "金融時系列解析"
author: "林 高樹"
date: "2023-11-11"
output:
 git_book:
   lib_idr: "libs"
documentclass: book
biblio-style: apalike
link-citations: yes
---

# はじめに

<!---
output:
 html_document:
   df_print: kable
   highlight: tango
   number_sections: no
   toc: no
   toc_depth: 2
   toc_float: no
   keep_md: no
   fig_caption: yes
documentclass: bxjsarticle
classoption: xelatex,ja=standard

title: "金融時系列解析"
author: "林 高樹"
date: "2023-11-11"
site: bookdown::bookdown_site
output: bookdown::html_document2: default
documentclass: bxjsbook
classoption: xelatex,ja=standard
bibliography: [book.bib, packages.bib]
biblio-style: apalike
link-citations: yes
github-repo: rstudio/bookdown-demo
description: "UNDER CONSTRUCTION"
-->

<!-- buildエラー(9/3/22時点): 
pdf文字化け中 →
classoption: xelatex,ja=standardを挿入
→LaTeX Error: File `ctexhook.sty' not found.
-->


## 時系列モデリング
- ターゲットの時系列データの特徴を表現できるような確率・統計モデルの構築
  - "stylized facts"等の中から, 重要な特徴にフォーカスする
  - 実際の時系列データと構築モデルの持つ特徴が類似するように
  - 時系列プロット, 散布図, ヒストグラム, 要約統計量等に関して
    - 理論計算やシミュレーション(パスの生成)
  - 金融時系列データに現れる特徴は, データ期間 (例, コロナ禍以前・以後) の他, データの観測頻度 (例, 月次, 週次, 日次, 5分次) にも依存
- 構築される時系列モデルに望まれる性質 (要件)
  - 記述性 (ターゲットの時系列データとの特徴の類似性の表現)
  - 解析容易性, 解釈可能性
  - 操作性・取扱容易性
  - 推定容易性, 計算効率性
  - モデル安定性・頑強性
  - 予測精度の高さなど
- 候補となるモデルは無数
  - 汎用的で, 良い性質を持つモデルクラスに関する研究
  - 時系列解析分野で中核を成すクラス →  線形+定常時系列過程
- モデル選択・推定, モデルの利用
  - ターゲット時系列の特徴を踏まえ. 適切なモデルクラスを選択する
  - データに対してモデルを適合し, パラメータを推定する
  - 推定モデルの妥当性をチェック (モデル診断)
  - 予測や制御等に利用
- 本授業で扱う対象・アプローチ
  - 目的変数(観測変数)自体が時系列構造を持ち, 時系列モデルで記述されるケースに主要な関心
  - 出発点(基本的設定): 1変量の線形・定常時系列過程
    → 金融時系列データは, 線形・定常時系列過程から乖離する特徴も有する
  - → 非線形 and/or 非定常な時系列過程へ
  - + 観測変数が多次元で時系列構造を持つケース (多変量時系列)
  - + 外部変数のあるケース
- 金融時系列解析におけるチャレンジ
  - 時系列データは, 確率過程の観点からは, 一本の"サンプルパス"の実現と見なせる
  - マクロ・ミクロの状態が 時間と共に変化する金融・経済時系列データは,  再現性の乏しいデータであると考えられる
  - さらに, 金融市場は過去に実現したデータに基づいて市場参加者が行動を変化させる
  - ある種の"法則性"を仮定しモデルにより表現することで, 金融市場においても"最適な"行動を取りたい






