
---
layout:     post
title:      "Java Annotation"
subtitle:   ""
date:       2015-02-01 12:00:00
author:     "Tidycoder"
header-img: "img/post-bg-2015.jpg"
tags:
    - Java
---

### annotation是何时起作用的？

The first main distinction between kinds of annotation is whether they're used at **compile time** and then discarded (like @Override) or placed in the compiled class file and available at **runtime** (like Spring's @Component). This is determined by the **@Retention** policy of the annotation. If you're writing your own annotation, you'd need to decide whether the annotation is helpful at runtime (for autoconfiguration, perhaps) or only at compile time (for checking or code generation).

### 内部工作原理

When compiling code with annotations, the compiler sees the annotation just like it sees other modifiers on source elements, like access modifiers (public/private) or final. When it encounters an annotation, it runs an **annotation processor**, which is like a plug-in class that says it's interested a specific annotation. **The annotation processor generally uses the Reflection API to inspect the elements being compiled and may simply run checks on them, modify them, or generate new code to be compiled**. @Override is an example of the first; it uses the Reflection API to make sure it can find a match for the method signature in one of the superclasses and uses the Messager to cause a compile error if it can't.

### annotation processor学习

There are a number of tutorials available on writing annotation processors; [here's a useful one.](http://travisdazell.blogspot.com/2012/10/writing-annotation-based-processor-in.html)  Look through the methods on the Processor interface for how the compiler invokes an annotation processor; the main operation takes place in the process method, which gets called every time the compiler sees an element that has a matching annotation.

refer to : http://stackoverflow.com/questions/35959379/how-does-java-runtime-annotation-work-internally