---
layout: post
title:  "cplusplus 11 random 헤더 공부한 내용"
subtitle:    "11부터 있는 random 헤더 공부 한 것"
categories: study
tags: c++
comments: true
---


#### 컴퓨터는 완전한 의미의 랜덤 숫자를 만들 능력이 없다.

#### 따라서 난수처럼 보이는 숫자를 만든 후 난수라고 말한다(수도 난수)

#### 난수처럼 보이는 숫자들을 만들기 위해 컴퓨터는 seed number를 받아서 정해진 공식대로 숫자를 만든다.

#### seed number는 공식에 들어가는 첫번째 숫자이다.

#### seed number가 고정되면 항상 동일한 난수가 나온다. 공식에 들어가는 첫번째 숫자가 똑같으므로 그 이후 과정에서 나오는 숫자도 동일하게 나오기 때문이다.



C++11부터 랜덤변수를 만드는 헤더로 <random> 헤더가 들어가 있다.

이 <random> 헤더의 사용법이 헷갈리므로 정리하려고한다.

<random> 헤더로 난수를 발생시 4가지 과정이 있다.

####  1) 랜덤 디바이스를 생성 (랜덤 디바이스를 통해 seed 값이 자동으로 바뀐다)
####  2) 생성기를 생성한다
####  3) 생성기가 따르는 분포를 설정한다
####  4) 분포가 생성기로 분포를 따르는 난수 생성를 생성한다

<br/>
<br/>

### 1) 랜덤 디바이스 생성


```c++
#include <iostream>
#include <random>
using namespace std;
int main()
{
	random_device re;
	return 0;
}
```

===> random_device 자체가 랜덤 디바이스를 뜻하고 re는 랜덤 디바이스의 변수명이다

<br/>
<br/>

### 2) 생성기를 생성한다

##### 생성기는 공식이라고 편하게 생각하겠다. 생성기 종류 별로 난수를 만드는 공식이 다른 것이다. 예를 들어 A 생성기는 X+Y로 난수를 만들지만 B 생성기는 X - Y로 난수를 만든다 이런식으로 생각하겠다.

https://www.cplusplus.com/reference/random/

위 홈페이지를 보면 generators에 엄청나게 많은 생성기들이 있다.
많은 생성기 중  default_random_engine을 생성기로 사용한다

```c++
#include <iostream>
#include <random>
using namespace std;
int main()
{
	random_device re;

	default_random_engine randE( re() );

	for (int i = 0; i < 2; i++)
	{
		cout << randE << endl;
	}
	return 0;
}
```

default_random_engine을 생성기로 사용하였으며 생성기의 변수명은 randE이다. 이때 생성기의 변수명 randE안에 랜덤 디바이스의 변수명 re()를 넣어줘야한다. 이제 랜덤한 seed를 이용해서 default_random_engine은 자신이 가진 공식을 사용해 (의사)난수를 생성한다.

 코드를 작동시키면 알 수 있겠지만 생성기의 변수명 만으로도 난수를 생성하여 cout 할 수 있다. 하지만 생성기가 따를 분포를 결정해 주지 않으면 정말 쓸모 없이 난수만 만들어진다. 따라서 생성기가 난수를 생성하며 만족시킬 분포를 결정해야 한다.

 <br/>
 <br/>

 ### 3) 생성기가 따르는 분포를 설정한다

마찬가지로 위 홈페이지를 가보면 다양한 종류의 분포를 만들어주는 함수를 알려주고 있다. 분포의 종류와 성질은 통계적인 내용이므로 후에 공부하자. 크게 보면 Uniform한 분포를 따르는 함수들, 베르누이와 관련된 분포를 따르는 함수들, Rate-based 분포를 따르는 함수들, 표준분포(가우시안) 관련된 분포를 따르는 함수들, Piecewise와 관련된 분포를 따르는 함수들을 정의하고 있다.

이 중 가우시안과 관련된 분포를 따르는 함수 중 normal_distribution 분포를 생성기가 따라야 하는 분포로 설정하겠다.

##### 분포중에서 베르누이 분포를 제외하고는 전부 class가 template으로 선언되어 있다.


```c++
#include <iostream>
#include <random>
using namespace std;
int main()
{
	random_device re;

	default_random_engine randE(re());

	normal_distribution<double> noraml(0, 100);

	return 0;
}
```


이제 randE라는 생성기가 만들어내는 변수는 평균이 0이고 분산이 100 (표준편차는 10)인 가우시안 함수의 분포를 만족하면서 생성 될 것이다.

<br/>
<br/>

### 4) 분포가 생성기를 사용해 분포를 따르는 난수들을 생성한다.



```c++
#include <iostream>
#include <random>
using namespace std;
int main()
{
	random_device re;

	default_random_engine randE(re());

	normal_distribution<double> noraml(0, 100);

	for (int i = 0; i < 100; i++)
	{
		cout << noraml(randE) << endl;
	}

	return 0;
}
```


normal(randE) 코드를 사용해 평균이 0이고 분산이 100인 가우시안 분포를 가지는 난수들을 생성할 수 있다.

random 헤더는 대략 위와 같이 사용하며 통계적 지식이 있어서 어떤 분포를 사용할지 또 어떤 생성기를 사용할지를 상황에 맞게 상뇽하면 될 것 같다.


한글로 된 MS 문서이다.

https://docs.microsoft.com/ko-kr/cpp/standard-library/random?view=msvc-160#engdist
