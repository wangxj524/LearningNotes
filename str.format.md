
# str.format() -- 增强的格式化字符串函数

## 一. 映射示例

#### 1. 通过位置


```python
'{0}, {1}'.format('site0', 'site1')
```




    'site0, site1'




```python
'{}, {}'.format('site0', 'site1')
```




    'site0, site1'




```python
'{1}, {0}, {1}'.format('site0', 'site1')
```




    'site1, site0, site1'



字符串的`format`函数可以接受不限个参数，位置可以不按顺序，可以不用或者用多次，不过2.6不能为空{}，2.7才可以。

#### 2. 通过关键词参数


```python
'{name}, {age}'.format(age=29,name='wxj')
```




    'wxj, 29'



#### 3. 通过对象属性


```python
class Person:  
    def __init__(self,name,age):  
        self.name,self.age = name,age  
    def __str__(self):  
        return 'This guy is {self.name},is {self.age} old'.format(self=self)

str(Person('wxj', 29))
```




    'This guy is wxj,is 29 old'



#### 4. 通过下标


```python
info_list = ['wxj', 29]
'{0[0]}, {0[1]}'.format(info_list)
```




    'wxj, 29'



## 二. 格式限定符

它有着丰富的的“格式限定符”（语法是{}中带:号）。

#### 1. 填充与对齐

- ^、<、>分别代表居中、左对齐、右对齐，后面跟宽度值。
- :号后面带填充的字符，只能是一个字符，不指定的话默认是用空格填充


```python
'{:>8}'.format('123')
```




    '     123'




```python
'{:0<8}'.format('123')
```




    '12300000'




```python
'{:^8}'.format('123')
```




    '  123   '



#### 2. 精度与类型f


```python
'{:.2f}'.format(3.14159265)
```




    '3.14'



其中.2表示长度为2的精度，f表示float类型。

#### 3. 数值进制与千分位分隔符

b、d、o、x分别是二进制、十进制、八进制、十六进制；而用‘，’号可以实现金额的千分位分隔。


```python
'{:b}'.format(17)
```




    '10001'




```python
'{:d}'.format(17)
```




    '17'




```python
'{:o}'.format(17)
```




    '21'




```python
'{:x}'.format(17)
```




    '11'




```python
'{:,}'.format(1234567890)
```




    '1,234,567,890'


