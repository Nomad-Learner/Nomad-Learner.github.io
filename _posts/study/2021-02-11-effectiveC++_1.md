---
layout: post
title:  "effective C++ 1"
subtitle:   "const, enum"
categories: study
tags: c++
comments: true
---


1. C++에서는 #define이 아니라 const, enum을 먼저 써야한다.


<pre>
<code>
#include<iostream>
using namespace std;

#define PI 3.141592

void Add(int a)
{

}

int main()
{
	PI;
	PI;
	Add(PI);
	PI;
	PI;

	return 0;
}




asfasasdfadfdasf
