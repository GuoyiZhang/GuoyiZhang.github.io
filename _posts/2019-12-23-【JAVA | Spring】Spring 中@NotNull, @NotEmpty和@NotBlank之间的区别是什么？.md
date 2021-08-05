---
layout:     post
title:      【JAVA | Spring】Spring 中@NotNull, @NotEmpty和@NotBlank之间的区别是什么？
subtitle:   【JAVA | Spring】Spring 中@NotNull, @NotEmpty和@NotBlank之间的区别是什么？
date:       2019-12-23 18:02:27
author:     Sunny day
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - Spring Boot
---

>【JAVA | Spring】Spring 中@NotNull, @NotEmpty和@NotBlank之间的区别是什么？

# 【JAVA | Spring】Spring 中@NotNull, @NotEmpty和@NotBlank之间的区别是什么？


示例：
String name = null; @NotNull: false @NotEmpty: false @NotBlank: false String name = ""; @NotNull: true @NotEmpty: false @NotBlank: false String name = " "; @NotNull: true @NotEmpty: true @NotBlank: false String name = "Great answer!"; @NotNull: true @NotEmpty: true @NotBlank: true

简述三者区别

@NotNull://CharSequence, Collection, Map 和 Array 对象不能是 null, 但可以是空集（size = 0）。 @NotEmpty://CharSequence, Collection, Map 和 Array 对象不能是 null 并且相关对象的 size 大于 0。 @NotBlank://String 不能是 null 且去除两端空白字符后的长度（trimmed length）大于 0。

----

### 注解的定义（在version 4.1中）：

1、@NotNull：

定义如下：
1

@Constraint

(validatedBy = {NotNullValidator.

class

})

这个类中有一个isValid方法是这么定义的：

1

2

3

public
 

boolean
 

isValid(Object object, ConstraintValidatorContext constraintValidatorContext) { 

 

return
 

object != 

null

;   

}

对象不是null就行，其他的不保证。

 

2、@NotEmpty：

定义如下：
1

2

@NotNull
   

@Size

(min = 

1

)  　　

也就是说，@NotEmpty除了@NotNull之外还需要保证@Size(min=1)，这也是一个注解，这里规定最小长度等于1，也就是类似于集合非空。

3、@NotBlank：
1

2

@NotNull
   

@Constraint

(validatedBy = {NotBlankValidator.

class

})  　

类似地，除了@NotNull之外，还有一个类的限定，这个类也有isValid方法：

1

2

3

4

if
 

( charSequence == 

null
 

) {  

//curious  

  

return
 

true

;    

}    

return
 

charSequence.toString().trim().length() > 

0

;

1

有意思的是，当一个string对象是

null

时方法返回

true

，但是当且仅当它的trimmed length等于零时返回

false

。即使当string是

null

时该方法返回

true

，但是由于

@NotBlank

还包含了

@NotNull

，所以

@NotBlank

要求string不为

null

。


