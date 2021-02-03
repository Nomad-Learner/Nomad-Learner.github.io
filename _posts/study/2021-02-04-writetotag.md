---
layout: post
title:  "C++ tag에 글쓰기"
subtitle:   "tag 활용"
categories: study
tags: C++
comments: true
---

C++ tag에 글 쓰기
글쓰기


1._featured 에서는


1.layout: list
2.title: etc
3.slug: etc
4.menu: true
5.submenu: false
6.order: 6
7.description: >
  기타 참고 내용들


이런식으로 소문자만 title과 slug에 다 적으면 되고

_featured_tags에서는


1.layout: tag-blog
2.title: OPENCV WITH C++
3.slug: opencv with c++
4.category: study
5.menu: false
6.order: 2

title은 대문자 slug는 동일한 이름의 소문자로 적어줘야한다.


tag에 맞춰서 글을 쓸 때는
categories는 _featured에 있는 소문자를 적어주고
tags는 _featured_tags에 있는 title을 적어주면 된다.(대문자로 적으면 된다.)


1.layout: post
2.title:  "C++ tag에 글쓰기"
3.subtitle:   "tag 활용"
4.categories: study
5.tags: C++
6.comments: true
