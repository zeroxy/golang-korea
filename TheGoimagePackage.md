# 요약 #
  * 내용 : Go언어 image 패키지
  * 원문 : [The Go image package](http://blog.golang.org/2011/09/go-image-package.html)
  * 번역자 : jeyong@gmail.com



# Introduction #
Go언어 image 패키지


# Details #
image 패키지 : Color, ColorModel와 Point, Rectangle, Image

# Color과 ColorModels #
Color는 interface로서 color와 관련된 type의 최소한의 method 집합을 정의한다.
CMYK나 YCbCr에서 색상 변환은 손실이 발생할 수 있다.
type Color interface{
> RGBA() (r, g, b, a uint32)
}