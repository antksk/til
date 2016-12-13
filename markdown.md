마크다운(*.md:MarkDown) 문법
====================================================
* 참고 : 
  - [basic-writing-and-formatting-syntax][basic-writing-and-formatting-syntax]
  - [organizing-information-with-tables][organizing-information-with-tables]
[basic-writing-and-formatting-syntax]: https://help.github.com/articles/basic-writing-and-formatting-syntax/
[organizing-information-with-tables]: https://help.github.com/articles/organizing-information-with-tables/

## 큰 제목 ####################################################################################
```
big header ('='문자 3개 이상 사용)
===
```

### [결과]
big header ('='문자 3개 이상 사용)
===

## 중간 제목 #################################################################################
```
middle header ('-'문자 3개 이상 사용)
---
```
### [결과]
middle header ('-'문자 3개 이상 사용)
---

## 글머리 (H1~6)  ###########################################################################
```
# H1
## H2
### H3
#### H4
##### H5
###### H6
```
### [결과]
# H1
## H2
### H3
#### H4
##### H5
###### H6

## 인용문(BlockQuote)  #######################################################################
```
> 인용문 한줄

인용문 + 다른 마크다운
> ### H3
> * List
>	```
>	code
>	```
```

### [결과]
> 인용문 한줄

인용문 + 다른 마크다운
> ### H3
> * List
>	```
>	code
>	```

> ### This is a H3
> * List
>	```
>	code
>	```

## 순서 있는 목록 ###########################################################################
```
1. 첫번째
2. 두번째
3. 세번째

순서가 바꿔도 "내림차순"으로 정렬됨
1. 첫번째
3. 세번째 -> 2. 세번째로 출력됨
2. 두번째
```
### [결과]
1. 첫번째
2. 두번째
3. 세번째

순서가 바꿔도 "내림차순"으로 정렬됨
1. 첫번째
3. 세번째 -> 2. 세번째로 출력됨
2. 두번째

## 다양한 순서 모양 ########################################################################
```
* 1단계
	- 2단계
		+ 3단계
		= 4단계
```
### [결과]
* 1단계
	- 2단계
		+ 3단계
		= 4단계
		
## 링크 ###################################################################################
```
[link keyword][id]
[id]: URL "Optional Title here"

Link: [Google][googlelink]
[googlelink]: https://google.com "Go google"
```
### [결과]
Link: [Google][googlelink]
[googlelink]: https://google.com "Go google"

## 이미지 ################################################################################
```
![Alt text](/path/to/img.jpg)
![Alt text](/path/to/img.jpg "Optional title")
```
![석촌호수 러버덕](http://cfile6.uf.tistory.com/image/2426E646543C9B4532C7B0)
![석촌호수 러버덕](http://cfile6.uf.tistory.com/image/2426E646543C9B4532C7B0 "RubberDuck")

사이즈 조절 기능은 없기 때문에 ```<img width="" height=""></img>```를 이용한다.