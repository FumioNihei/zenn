---
title: "Pillowで画像をマスク画像に従ってクロップ"
emoji: "🧯"
type: "tech"
topics: [Pillow,python]
published: true
published_at: 2023-12-07 01:17
---

四角形のアイコン画像の角を丸めたい，そんな時ありますよね．

[この辺](https://note.nkmk.me/python-pillow-image-crop-trimming/)とか[この辺](https://qiita.com/11kenterada/items/4d05593dd7e09c697e90)とか参照したうえで，より良い方法を検討した結果を報告します．


# 解法

画像を2枚用意します．一つがマスク (`mask.png`) ，一つはクロップする対象 (`image.png`) です．二つの高さと幅は一致している必要があります．

```python
import numpy as np
from PIL import Image

mask = Image.open( r"mask.png" )
mask = np.array( mask )

img = Image.open( r"image.png" )
img = np.array( img )

img[:, :, 3] = mask[:, :, 3]
img = Image.fromarray( img )
img.save( r"result.png" )
```

結果は以下です．

|マスク画像|クロップ対象|結果|
|:--|:--|:--|
|![マスク画像](/images/pillow-crop-by-mask/mask.png)|![クロップ対象](/images/pillow-crop-by-mask/image.png)|![結果](/images/pillow-crop-by-mask/result.png)|

クロップ対象の画像の角が，マスク画像に従って丸まりました．かわいい．

# 解説

やっていることは単純で，以下の行で：

```python
img[:, :, 3] = mask[:, :, 3]
```

マスク画像のアルファチャンネルを，クロップ対象画像のアルファチャンネルに置き換えているだけです．

マスク画像の透明なピクセルが，元画像の対応する場所のピクセルが透明になる，といった形です．

# 応用

マスク画像を複雑にすれば，以下のようにくりぬくこともできます．


|マスク画像|クロップ対象|結果|
|:--|:--|:--|
|![マスク画像](/images/pillow-crop-by-mask/mask2.png)|![クロップ対象](/images/pillow-crop-by-mask/image.png)|![結果](/images/pillow-crop-by-mask/result2.png)|


さらに，アルファチャンネルを反転させて置き換えることで，

```python
img[:, :, 3] = (255 - mask[:, :, 3])
```


以下のような感じにもできます．

|マスク画像|クロップ対象|結果|
|:--|:--|:--|
|![マスク画像](/images/pillow-crop-by-mask/mask2.png)|![クロップ対象](/images/pillow-crop-by-mask/image.png)|![結果](/images/pillow-crop-by-mask/result3.png)|


アルファチャンネルの値は0-255をとるので，透明度をグラデーションさせたマスクを用意すれば，

|マスク画像|クロップ対象|結果|
|:--|:--|:--|
|![マスク画像](/images/pillow-crop-by-mask/mask3.png)|![クロップ対象](/images/pillow-crop-by-mask/image2.png)|![結果](/images/pillow-crop-by-mask/result5.png)|

色々できます．

# 終わりに

よく考えたらそりゃそうだろ，って感じですよね．

皆さんも角を丸めて楽しんでください．