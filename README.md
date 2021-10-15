# MyString
## Code
>
``` 
class MyString{
  char* string; //pointer to string
  int length;
  int capacity;
public:
  MyString(const char* str);
  MyString(const MyString& str);
  char& operator[](int index);
  void reserve(int size);
  MyString& assign(const MyString& str);
  MyString& insert(int index, const MyString& str);
  MyString& erase(int from, int to);
  MyString operator+(const MyString& str) const;
  const MyString& operator=(const MyString& str);  
  void print() const;
  void println() const;
  void printinfo() const;
};
```

## Note

## MyString operator+(const MyString& str) const;
>
+연산자는 피연산자, 즉 oper+ 함수를 가진 객체 그리고 매개변수로 받은 객체의 값을 바꾸지 않는다. 그러므로 const 함수로 선언 가능하고 const형으로 매개변수를 받을수있다. 반환형이 레퍼러스인경우는 보통 2가지이다. 매개변수를 레퍼런스로받아 그대로 리턴할경우 아니면 매개변수의 레퍼런스말고 객체 자신 혹은 자신의 맴버변수를 리턴할경우이다. +연산자의 경우 피연산자 두값(함수의주인객체, 매개변수 객체)을 더해서 새로운 객체를 리턴해야한다. 왜냐하면 +연산자는 피연산자에 영향을 끼치지 않기때문이다. 억지로 레퍼런스를 리턴하기위해서 함수내에서 
```
MyString& MyString::operator+(const MyString& str) const
{
  MyString temp = *this;
  temp.insert(length, str);
  return temp;
}
```
라고 쓴다면 유효하지않다. b는 스택에 들어가서 함수종료후에 사라지고 레퍼런스의 값은 쓰레기값이 된다. 하지만 예를들어
```
MyString& MyString::operator+(const MyString& str) const
{
  MyString* temp = new MyString(*this);
  temp.insert(length, str);
  return temp;
}
```
라고 작성하면 리턴값은 유효하지만 함수외부에서 동적할당 했으니 free 시켜줘야한다 그런복잡한 과정을 생략한것이
```
MyString MyString::operator+(const MyString& str) const
{
  MyString temp = *this;
  temp.insert(length, str);
  return temp;
}
```
이다 이는 함수내에서 새로운 객채를만들고 그객체를 메모리상에 다시복사하여 임시객체를 만든다 그리고 그 임시객체의 레퍼런스를 반환한다. 그리고 그 레퍼런스를 사용하지않을때는 알아서 free 해준다.

## MyString& operator=(const MyString& str);
>
+operator 와 달리 새로운 객체가 필요하지않다 함수몸체주인객체의 값을 바꾸고 그 값을 함수 외부에서 복사, 혹은 읽기위해 적절하게 반환해야한다. 레퍼런스로 반환할수있고 그냥 일반형으로 반환할수 있지만 이경우에는 불필요한 임시객체의 생성을 막기위해 레퍼런스 타입으로 반환한다. 그 레퍼런스를 함수외부받는곳에서 바꾸기 싫다면, 예를들어 
```
MyString str = "ab";
MyString str1 = "c";
MyString str2 = str+str1; //abc
(str = str1) = str2;
```
와같은 비상식적인 연산을 피고하싶다면 const MyString&를 반환형으로 취하면 된다. 그러면 불필요한 임시객체의 생성도 막고 그 레퍼런스를 순전히 읽기 용도로만 쓸수있다.
```
(str = str1) = str2;
```
대신에
```
str = str1;
str = str2;
```
와같은 연산만 가능해진다. 즉 다음줄로 넘어가기 전까진 무조건 상수화 된다. 하지만 다음줄로 넘어가면 그제한이 풀린다는것을 알수있다. 비슷한 윈리로 임시객체의 값을 함수외부에서 바꾸기 싫다면 const MyString 를 반환값으로 설정하면 된다. 하지만
```
str = str2 = str1;
```
와 같은행위는 여전히 가능하다. str2 = str1의 연산을 먼저 수행하고 상수화된 str2는 다시 str의 = 연산자의 매개변수가 되어 str2의 값은 바뀌지않기 때문이다. 하지만 여기서 = 연산자 매개변수형을 (const MyString& str) 에서 (MyString& str)로 변경한다면 
```
str = str2 = str1;
```
는 오류가난다. str2 = str1에서 str2은 상수화 되고 상수화된 str2는 MyString& str 의 매개변수가 될수없다.
## char& operator[](int index);
>
객체와 마찬가지로 특정한 인덱스의 값을 바꾸려면 레퍼런스를 반환형으로 취해야한다. 반환형을 일반형으로 취한다면
```
cout << str[0];
```
과같은 읽기 연산은 가능하겠지만
```
str[0] = 'k';
```
와 같은 쓰기연산은 불가능해진다.
## 정리
1. (str1 + str2) = str3 와같은 연산을 막아주는것, 즉 str1 + str2 의값을 상수화 시켜주는것은  +연산자의 반환값이다.
2. str1를 상수화 시켜주는것은 함수
3. str2를 상수화 시켜주는것은 매개변수.


