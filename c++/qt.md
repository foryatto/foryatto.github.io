# 教程

- [Qt 学习之路 2](https://www.devbean.net/2012/08/qt-study-road-2-catelog/)



# 信号与槽

信号和槽其实是观察者模式的一种实现。

当某个事件发生之后，比如，按钮检测到自己被点击了一下，它就会发出一个信号（signal）。这种发出是没有目的的，类似广播。**如果有对象对这个信号感兴趣，它就会使用连接（connect）函数，意思是，将想要处理的信号和自己的一个函数（称为槽（slot））绑定来处理这个信号。**也就是说，当信号发出时，被连接的槽函数会自动被回调。

- signals 关键字：最终被#define 置换为一个访问控制符，其简化后为 #define signals public
- slots 关键字：最终被#define 置换为一个空宏，即简化后为 #define slots
- emit 关键字：同样被#define 置换为一个空宏，即简化后为 #define emit



只有 QObject 及其派生类才能使用信号和槽机制，且在类之中还需要使用 Q_OBJECT 宏。

## 信号创建规则

信号使用 **signals** 关键字声明，在其后面有一个冒号`:`，**在其前面不能有 public、private、protected 访问控制符**，信号默认是 public 的。

信号只需像函数那样声明即可，其中可以有参数，参数的主要作用是用于和槽的通信，这就像普通函数的参数传递规则一样。信号虽然像函数，但是对他的调用方式不一样，信号需要使用 emit 关键字发射。

信号只需声明，不能对其进行定义，信号是由 moc 自动生成的。

信号的返回值只能是 void 类型的。

## 槽创建规则

声明槽需要使用 slots 关键字，在其后面有一个冒号`:`，且槽需使用 public、private、protected 访问控制符之一。

槽就是一个普通的函数，可以像使用普通函数一样进行使用，槽与普通函数的主要区别是，槽可以与信号关联。

## 发射信号规则

发射信号需要使用 emit 关键字，注意，在 emit 后面不需要冒号。

emit 发射的信号使用的语法与调用普通函数相同，比如有一个信号为 void f(int)，则发送的语法为：emit f(3); 

当信号被发射时，与其相关联的槽函数会被调用。(注意：**信号和槽需要使用QObject::connect 函数进行关联之后，发射信号后才会调用相关联的槽函数**)

因为信号位于类之中，因此发射信号的位置需要位于该类的成员函数中或该类能见到信号的标识符的位置。



## 信号和槽的关系

槽的参数的类型需要与信号参数的类型相对应，

槽的参数不能多余信号的参数，因为若槽的参数更多，则多余的参数不能接收到信号传递过来的值，若在槽中使用了这些多余的无值的参数，就会产生错误。

**若信号的参数多余槽的参数，则多余的参数将被忽略。**

一个信号可以与多个槽关联，多个信号也可以与同一个槽关联，信号也可以关联到另一个信号上。

**若一个信号关联到多个槽时，则发射信号时，槽函数按照关联的顺序依次执行。**

**若信号连接到另一个信号，则当第一个信号发射时，会立即发射第二个信号。**

```c++
//头文件 m.h 的内容
class A : public QObject{ //信号和槽必须继承自 QObject 类
  Q_OBJECT                    //必须添加该宏
  
  //public signals:void s1(int);  //错误 signals 前不能有访问控制符。
  
  signals:void s();//使用 signals 关键字声明信号，信号的语法与声明函数相同。
  signals:void s(int,int);//正确，信号可以有参数，也可以重载。
  
  //void s2(){} //错误，信号只需声明，不能定义。
  
  void s3(); //注意：这仍是声明的一个信号
public://信号声明结束后，重新使用访问控制符，表示以下声明的是成员函数。
  void g(){
    emit s3(); //发射信号
  }
};
class B:public QObject{
  Q_OBJECT
      
public slots:               //使用 slots 关键字声明槽
  void x(){
    cout<<"X"<<endl;
  }
  //slots: void x(){}     //错误，声明槽时需要指定访问控制符。
  public:
  void g(){ 
    // emit s3();       //错误，在类 B 中对于标识符 s3 是不可见的
  }
};

//源文件的内容
int main(int argc, char *argv[]){
  A ma; 
  B mb;
  QObject::connect(&ma,&A::s3,&mb,&B::x); //关联信号和槽
  ma.g(); //调用g发送信号
  return 0;
}
```

## 断开信号与槽的连接

**QObject::disconnect()**



# QTextCodec

字符编码转换

俄文 KOI8-R -> Unicode

```cpp
QByteArray encodedStr = "......";
QTextCodec *codec = QTextCodec::codecForName("KOI8-R");
QString str = codec->toUnicode(encodedStr);
```

Unicode -> 俄文 KOI8-R

```text
QString str = "......";
QTextCodec *codec = QTextCodec::codecForName("KOI8-R");
QByteArray encodedStr = codec->fromUnicode(str);
```



# QString/QList/





# exec()

`exec()`函数的真正含义是开启一个新的事件循环（我们会在后面的章节中详细介绍有关事件的概念）。所谓事件循环，可以理解成一个无限循环。Qt 在开启了事件循环之后，系统发出的各种事件才能够被程序监听到。这个事件循环相当于一种轮询的作用。既然是无限循环，当然在开启了事件循环的地方，代码就会被阻塞，后面的语句也就不会被执行到。因此，对于使用了`exec()`显示的模态对话框，我们可以在`exec()`函数之后直接从对话框的对象获取到数据值。

```c++
QDialog dialog(this);
if (dialog.exec() == QDialog::Accepted) {
    // do something
} else {
    // do something else
}
```

