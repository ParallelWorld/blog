---
title: json转map精度丢失
date: 2017-11-20 13:59:59
tags:
categories:
---

# 问题描述
支付返回结果（json字符串）先转map再转对象时发生了精度丢失。
字符串是`{"id":188838035603143129}`。转成map，再转成对象时，对象对应的long类型字段变成`188838035603143130`。
使用的是gson.fromJson(String, Class)。gson版本是2.5。

# 问题原因
debug发现，在string转成map时，值已经发生了变化。gson默认是将字符串中的number转换成double类型。
原string串`188838035603143129`，map中的value是1.88838035603143136E17，此时已经发生了精度丢失。

对比看
 188838035603143129
1.88838035603143136E17

## long->double精度丢失的原因
浮点型（float,double）在内存中都是以科学计数法存储。
以double双精度举例，内存中的结构是
  1    11     52
符号位 指数位 尾数部分
2^52 = 4503599627370496，一共16位。

float的精度是6-7位有效数字，6位绝对是精确的。
double的精度是15-16位有效数字，15位是绝对精确的。
18位的id字符串转成double时，存储的结果本该是1.88838035603143129e17，但是由于精度问题，准确的只有15位也就是888380356031431，后面的两位是截断后的结果，并不准确。
对比实际调试时的结果：
1.88838035603143129e17---理想情况
1.88838035603143136e17---实际情况
小数点后15位是准确的，再往后不再准确。

因此当long型的位数不大于15位时，先转double，再转回long，精度不会丢失。但是大于15位时，先转double，再转回long，精度会丢失。

## gson转换json串中的number为double的原因
除了精度丢失的问题外，gson将json串中的long转成double也是不合理的。

在gson的`ObjectTypeAdapter`类中可以看到：
```
@Override public Object read(JsonReader in) throws IOException {
    JsonToken token = in.peek();
    switch (token) {
    case BEGIN_ARRAY:
      List<Object> list = new ArrayList<Object>();
      in.beginArray();
      while (in.hasNext()) {
        list.add(read(in));
      }
      in.endArray();
      return list;
 
    case BEGIN_OBJECT:
      Map<String, Object> map = new LinkedTreeMap<String, Object>();
      in.beginObject();
      while (in.hasNext()) {
        map.put(in.nextName(), read(in));
      }
      in.endObject();
      return map;
 
    case STRING:
      return in.nextString();
 
    case NUMBER:
      return in.nextDouble();
 
    case BOOLEAN:
      return in.nextBoolean();
 
    case NULL:
      in.nextNull();
      return null;
 
    default:
      throw new IllegalStateException();
    }
  }
```
在case NUMBER这一行可以看到，数值型的数据都被简单地处理为Double型的值。



# 解决办法
不经过map转换，直接将json串转换成对象。
如果非要转成map，那么可以使用其他的json库，比如jackson和fastjson。


# 参考链接
[http://www.jianshu.com/p/c51041a791bd]()
[http://www.zhyea.com/2016/11/13/gson-map-integer-double.html]()
