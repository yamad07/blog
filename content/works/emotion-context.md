---
title: "Survey: Context-aware Emotion Recognition"
date: 2020-08-08T19:20:18+09:00
draft: true
---

# はじめに
本項では，Context-aware Emotion Recognitionという研究領域に取り組んだ研究と，それの派生研究に関するまとめをします．

## 導入

「感情とは何なのか」の話

## Context-aware Emotion Recognition
客観的に人の感情を理解するためには，その人の表情だけでなく，その人を取り巻く周りの状況や文脈をもとに推論をする必要があります．

この「文脈をもとに感情を推定する」ということを機械学習を用いて行うことができれば，ロボティクスなどへの応用を行うことで，人とコンピュータの新たなインタラクションを生み出すことができるようになります．

## EMOTIC

この研究分野に一番最初に取り組んだとされる研究が，[Emotion Recognition in Context](https://openaccess.thecvf.com/content_cvpr_2017/papers/Kosti_Emotion_Recognition_in_CVPR_2017_paper.pdf)です．この研究では，コンテキストを考慮した画像の大規模なデータセットの作成と，それを用いてニューラルネットワークを学習させ，ベースラインを作成しています．

<img src="https://i.gyazo.com/f552a9fc261e66cf2ab4b1343a748cda.png" alt="EMOTICのデータセットの例" style="zoom:33%;" />

このデータセットでは，画像に対して離散的な感情のカテゴリと連続的な感情値がラベルとして紐づいています．感情のカテゴリは，26種類から選択されており，それぞれに対する定義も名義されています．連続的な感情値は，VADモデルと呼ばれる心理学で提唱されているモデルに基づいており，Valance・Arousal・Dominanceの3つの値から構成されています．

<img src="https://i.gyazo.com/8f9eca6025df005fe8a5a9d79e04c7a5.png" alt="EMOTICのデータセットのラベル" style="zoom:33%;" />

このデータセットを使って，画像から感情のカテゴリとVADを予測するモデルを学習します．アーキテクチャは以下のようなものであり，人の領域のみを切り取った画像と，全体の画像を別々のネットワークにいれ，最後に結合して予測を行います．

![EMOTICを用いたモデルのアーキテクチャ](https://i.gyazo.com/c8f54097b49c4ddfe411ca9743efd41b.png)

実験では，Average Precisionを用いてそれぞれの感情のラベルにおいてどれくらいのPrecisionを達成できたかを評価します．比較として，全体の画像のみをいれた場合と人の領域のみをいれた場合，両方を用いた場合でそれぞれ実験を行なっています．

![離散的なカテゴリの評価](https://i.gyazo.com/9d39a6d0490c6fc648a81250ceec35d4.png)

論文内では，比較結果からもわかるとおり全体の画像と人の領域をいれることで精度を向上させることができると主張しています．ですがいくつかのクラスでは低いAverage Precisionを達成してしまっていて，それに関する具体的な考察や改善できる方法については言及されていませんでした．

## 工学的なアプローチによる改善

上記の手法を大きく改善し，SOTAを達成したのが[Context-Aware Generation-Based Net for Multi-Label Visual Emotion Recognition](https/ieeexplore.ieee.org/Xplore/login.jsp?reason=notIncluded&url=http%3A%2F%2Fieeexplore.ieee.org%2Fstamp%2Fstamp.jsp)という研究です．この研究では主にニューラルネットワークの研究において有効性が示されている機構を導入しアーキテクチャの改善を行なっています．アテンションとそれを有効活用するためのGRUの導入を行いCAGBNという新たなモデルを提案しています．

アーキテクチャの図は以下です．EMOTICと同様に人領域の画像と全体の画像をそれぞれ別々のネットワーク(ResNet-50)に入力し，それに対してAttentionをかけて画像をエンコードします．エンコードされた特徴量に対して，感情のラベルの分散表現を結合させ，GRUに入力するという流れを，クラスごとに行います．その結果を最終的にSoftmax関数に入力し，予測の分布を得ます．

![CAGBNのアーキテクチャ](https://i.gyazo.com/b65a282b1a168691fbe49fe5c9e0c7f0.png)

結果は以下です．EMOTICの実験結果のようなクラスごとのAverage Precisionはないのですが，Mutl-Classの評価としてベーシックな二種類のF1スコアを出しています．EMOTICの手法をおおきく改善できていることがわかります．

<img src="https://i.gyazo.com/919e1c20f5f69c7484ea0b86c0fab9db.png" alt="CAGBNのF1スコア" style="zoom:50%;" />

CAGBNで提案されたいくつかの機構について詳しく見ていきます．先ずは感情のラベルの分散表現をt-SNEで二次元に落として可視化したものです．

![分散表現のベクトルをt-SNEで可視化したときの分布](https://i.gyazo.com/6f66e34c8c3c181780371810af20539e.png)

これを見ると，意味的に近い感情は，分散表現の上でもかなり近くなっていることがわかり，画像についているラベルから感情の概念をうまく獲得できていることがわかります．

アテンションが実際に画像内のどこに注目しているかを見ると，感情のラベルごとに違う領域を見ていることがわかります．

<img src="https://i.gyazo.com/e3531df4c5e02920348f9290f3c4e24c.png" alt="クラスごとのアテンションの可視化" style="zoom:50%;" />



## 心理学的なアプローチによる改善

CAGBNとは違い，心理学で提唱されている概念をうまくニューラルネットワークの学習に取り入れようとしているのが，[EmotiCon: Context-Aware Multimodal Emotion Recognition using Frege’s Principle](https://openaccess.thecvf.com/content_CVPR_2020/papers/Mittal_EmotiCon_Context-Aware_Multimodal_Emotion_Recognition_Using_Freges_Principle_CVPR_2020_paper.pdf)という研究です．Fregeの原則というのに従ってContext-Aware Emotional Recognitionを行うというアプローチです．

Fregeの原則とは，「言葉の意味を単独で問われるべきではない．文章などの文脈でのみ問われるべきである．」というものです．これを今回のタスクに落とし込むと，「人の感情はその文脈でのみ問われるべきである」ということになります．この考え方を用いて，画像から得られる多くの文脈を入力して感情の予測を行うのは，この研究のアプローチです．

実際には以下のようなアーキテクチャを提案しています．別の学習済みモデルで予測をした深度の情報や骨格の情報を入力して予測をするモデルです．

![文脈を考慮した学習](https://i.gyazo.com/dd14c6fbe9d8f726eaafbd0098813deb.png)

このように，多くの文脈の情報を考慮した上で学習をしたモデルを，他の手法と比較をします．今回はEMOTICの評価と同様，クラスごとのAverage Precisionを行います．結果としては，多くのクラスで最も良い精度を達成することができています．

<img src="https://i.gyazo.com/8f1ccf8cac8d3d2b3cb63abb85f36a4c.png" style="zoom:33%;" />

また，今回は複数の文脈情報を入力することを提案しているので，文脈情報においてAblation Studyを行なっています．結果としては，複数の文脈情報を入れた場合が一番Average Precisionが高くなることがわかりました．

![EmotiConにおける文脈情報のAblation Study](https://i.gyazo.com/b5a85326bec74a029490a7be1e16300e.png)



## まとめ

今回はContext-aware Emotion Recognitionに関する研究をまとめました．複数のアプローチで手法が改善されていることがわかります．

EMOTIC以外の手法ではVADモデルによる評価は行われていませんでした．単純に精度が出なかったからなのか，正確なことはわかりませんが，おそらく離散的な感情の表現にはある程度の限界があるというのと，音楽のデータなどと組み合わせてマルチモーダルな学習をしたい時は，VADモデルによる予測値を有効活用できるという点で，今後はVADの抜本的な精度改善がなされることもありそうです．