# python_text_Aggregation
文字の集合体で文字を書く

## 掛け軸をみて、小さな文字を集合体のようにして、一つの一つの文字を書くような技法があることをしりました。pythonで、作ってみたのでコードを共有します。

サンプル画像

![こんにちはサンプル画像](konnitiwa_sample.png)


```python
import PIL.Image
import PIL.ImageDraw
import PIL.ImageFont
import matplotlib.pyplot as plt
import numpy as np
import codecs
import os
```
# 縦書きまたは横書きの文字画像を作成

```python
imagePath = "image.png"
ttfontname = "C:\\Windows\\Fonts\\meiryob.ttc"
outputtextpath = 'output.txt'

def make_text_Image(text,fontsize=12,isVartical=True):
    """
    converting text to images

    This code converts character text into images and supports both vertical and horizontal writing.
    """

    margin = int(fontsize / 3)
    textLength = len(text)
    canvasSize = (fontsize,fontsize)
    if isVartical:
        canvasSize = (fontsize+margin,fontsize*textLength + margin)
    else:
        canvasSize = (fontsize*textLength +margin,fontsize +margin)
    backgroundRGB = (255, 255, 255)
    textRGB       = (0, 0, 0)
    img  = PIL.Image.new('RGB', canvasSize, backgroundRGB)
    draw = PIL.ImageDraw.Draw(img)
    font = PIL.ImageFont.truetype(ttfontname, fontsize)
    if isVartical:
        for i in range(textLength):
            topleft = (canvasSize[0]//2-fontsize//2,fontsize*i)
            draw.text(topleft, text[i], fill=textRGB, font=font)
    else:
        for i in range(textLength):
            topleft = (fontsize*i, canvasSize[1]//2-fontsize//2-margin//2)
            draw.text(topleft, text[i], fill=textRGB, font=font)
    img.save(imagePath)
```

# 文字の集合体で文字を書く関数
```python
def print_text(imagearray:np.ndarray,myword:str) -> None:
    """
    Create a collection of characters as a text file
    """
    count = 0
    def word_out(value:int,myword:str):
        """
        At coordinates where there are character lines in the image, 
        the string data is returned,
         and full-width spaces are returned where there are no characters.
        """
        mywordcount = len(myword)
        nonlocal count
        if value > 250:
            return "　"
        else:
            if count == mywordcount:
                count = 0
            count += 1
            return myword[count-1]
    
    if os.path.isfile(outputtextpath):
        os.remove(outputtextpath)

    for item in imagearray:
        mystr = "".join([word_out(i,myword=myword) for i in item])
        print(mystr, file=codecs.open(outputtextpath, 'a', 'utf-8'))
```

# 実行　画像の作成・表示
ここでは、「こんにちは」という文字の集合体で、「こんにちは」という文字を書いています。「こんにちは」の文字列をすきに変更してくだい。歴史資料ですと、お経などの文言を、小さな文字の集合体にして、その集合体で美術作品をつくるということがあります。それに似せて文字列で表現することができます。githubでは、右から文字を書くｻﾝﾌﾟﾙも作ってます。

```python
make_text_Image(text="こんにちは",fontsize=20)
im = PIL.Image.open(imagePath)
im = im.convert('L')
plt.gray()
im_array = np.asarray(im)
plt.imshow(im_array)
# こんにちは、という言葉の集合体で、文字を描いていく。
print_text(imagearray=im_array,myword="こんにちは")
```
