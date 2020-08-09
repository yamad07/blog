---
title: "Survey: Context-aware Emotion Recognition"
date: 2020-08-08T19:20:18+09:00
draft: true
---

# はじめに
本項では，Context-aware Emotion Recognitionという研究領域に取り組んだ研究と，それの派生研究に関するまとめをします．

## 導入

コンピュータで感情のデータを扱うために，まずは感情とは何かを明確に定義・モデル化して解釈することが必要になります．心理学の研究では，感情のモデルとして二次元のものと三次元のものが提唱されています．三次元のものは，プルチックの輪というものが提唱されていますが，コンピュータ上で扱う上では二次元のものの方がメリットが多いため，二次元の感情モデルのみを取り扱います．

二次元の感情モデルは，ラッセルとバレットが提唱したValence Arousalモデルというものです．二人は，「感情は色と同じように共通する属性によって表現される」と考え，感情値と覚醒度の二次元によって感情を表すモデルを提唱しました．感情値と覚醒度の連続値によって感情の概念が一意に定まるというものです．

このモデルの一番のメリットは，感情の距離を測ることができることです．全ての感情を二次元上にマッピングすることができるので，怒りと喜びの感情の距離がどれくらいか，をユークリッド距離で簡単に計算することができます．

## Context-aware Emotion Recognition
客観的に人の感情を理解するためには，その人の表情だけでなく，その人を取り巻く周りの状況や文脈をもとに推論をする必要があります．

この「文脈をもとに感情を推定する」ということを機械学習を用いて行うことができれば，ロボティクスなどへの応用を行うことで，人とコンピュータの新たなインタラクションを生み出すことができるようになります．

そこで，画像内の特定の人の感情を，周りの文脈から推定するタスクとして，Context-aware Emotion Recognitionが確立されるようになりました．

## EMOTIC

この研究分野に一番最初に取り組んだとされる研究が，[Emotion Recognition in Context](https://openaccess.thecvf.com/content_cvpr_2017/papers/Kosti_Emotion_Recognition_in_CVPR_2017_paper.pdf)です．この研究では，コンテキストを考慮した画像の大規模なデータセットの作成と，それを用いてニューラルネットワークを学習させ，ベースラインを作成しています．

![](https://i.gyazo.com/f552a9fc261e66cf2ab4b1343a748cda.png)

このデータセットでは，画像に対して離散的な感情のカテゴリと連続的な感情値がラベルとして紐づいています．感情のカテゴリは，26種類から選択されており，それぞれに対する定義も名義されています．連続的な感情値は，Valence ArousalモデルにDominanceを加えた，VADモデルに基づいており，Valance・Arousal・Dominanceの3つの値から構成されています．

<img src="https://i.gyazo.com/8f9eca6025df005fe8a5a9d79e04c7a5.png" alt="EMOTICのデータセットのラベル" style="zoom:33%;" />

このデータセットを使って，画像から感情のカテゴリとVADを予測するモデルを学習します．アーキテクチャは以下のようなものであり，人の領域のみを切り取った画像と，全体の画像を別々のネットワークにいれ，最後に結合して予測を行います．

<img src="https://i.gyazo.com/c8f54097b49c4ddfe411ca9743efd41b.png" alt="EMOTICを用いたモデルのアーキテクチャ" style="zoom:50%;" />

実験では，Average Precisionを用いてそれぞれの感情のラベルにおいてどれくらいのPrecisionを達成できたかを評価します．比較として，全体の画像のみをいれた場合と人の領域のみをいれた場合，両方を用いた場合でそれぞれ実験を行なっています．

結果としては，全体の画像と人の領域を両方用いたモデルの精度が最も高いという結果になりました．文脈の情報を考慮するために別々の特徴量を抽出していることが，高い精度に起因しているという見方ができます．

## 文脈情報への注目

上記の手法はあくまでも全体の画像と人の領域でしたが，全体の画像に人の領域の画像が含まれてしまっているため，全体の画像において人の領域以外の部分を考慮できているとは一概には言えません．

そこで，顔の領域の画像とと顔以外の領域の画像に分けてそれぞれから特徴量を抽出する手法が提案されました([Context-Aware Emotion Recognition Networks](https://arxiv.org/pdf/1908.05913.pdf))．この手法を用いることで，人の顔以外の文脈も十分に考慮されることが期待できます．

以下の図は実際に顔以外の領域の画像において，ニューラルネットワークがどこに注目しているかを可視化したものです．人の顔の領域をマスクした画像においては，別の人に対して注目がいっていることがわかります．

![CAER-Netの注目領域](https://i.gyazo.com/0fa3084c9793fce45edf250d2c6400d8.png)

## 文脈情報の追加

より文脈情報を加えることで精度を高めるアプローチをとっているのが[EmotiCon: Context-Aware Multimodal Emotion Recognition using Frege’s Principle](https://openaccess.thecvf.com/content_CVPR_2020/papers/Mittal_EmotiCon_Context-Aware_Multimodal_Emotion_Recognition_Using_Freges_Principle_CVPR_2020_paper.pdf)の研究です．Fregeの原則に従ってContext-Aware Emotional Recognitionを行うというアプローチです．

Fregeの原則とは，「言葉の意味を単独で問われるべきではない．文章などの文脈でのみ問われるべきである．」というものです．これを今回のタスクに落とし込むと，「人の感情はその文脈でのみ問われるべきである」ということになります．この考え方を用いて，画像から得られる多くの文脈を入力して感情の予測を行うのは，この研究のアプローチです．

実際には以下のようなアーキテクチャを提案しています．別の学習済みモデルで予測をした深度の情報や骨格の情報を入力して予測をするモデルです．

![文脈を考慮した学習](https://i.gyazo.com/dd14c6fbe9d8f726eaafbd0098813deb.png)

このように，多くの文脈の情報を考慮した上で学習をしたモデルを，他の手法と比較をします．今回はEMOTICの評価と同様，クラスごとのAverage Precisionを行います．結果としては，多くのクラスで最も良い精度を達成することができています．

また，今回は複数の文脈情報を入力することを提案しているので，文脈情報においてAblation Studyを行なっています．結果としては，複数の文脈情報を入れた場合が一番Average Precisionが高くなることがわかりました．

これらの結果から，人の感情を予測するためには，文脈情報を色々な方法で入力することで，予測の精度をあげることができるということがわかります．



## まとめ

今回はContext-aware Emotion Recognitionに関する研究をまとめました．基本的には対象の人以外の文脈をどのように追加するか，のアプローチで手法が改善されていることがわかります．

個人的には，同じ画像の中の別の人の感情がどれくらい正確に予測できているのか，が気になりました．この精度がかなり高くなれば，同じ空間にいる違う人のそれぞれの感情に応じて，最適な情報やインタラクションができるようになるため，新しいインターフェースを作ることができるかもしれません．

## 参考

- [Plutchik’s Wheel of Emotions](https://www.6seconds.org/2017/04/27/plutchiks-model-of-emotions/)
- [Emotion Recognition in Context](https://openaccess.thecvf.com/content_cvpr_2017/papers/Kosti_Emotion_Recognition_in_CVPR_2017_paper.pdf)
- [Context-Aware Generation-Based Net for Multi-Label Visual Emotion Recognition](https/ieeexplore.ieee.org/Xplore/login.jsp?reason=notIncluded&url=http%3A%2F%2Fieeexplore.ieee.org%2Fstamp%2Fstamp.jsp)
- [EmotiCon: Context-Aware Multimodal Emotion Recognition using Frege’s Principle](https://openaccess.thecvf.com/content_CVPR_2020/papers/Mittal_EmotiCon_Context-Aware_Multimodal_Emotion_Recognition_Using_Freges_Principle_CVPR_2020_paper.pdf)
