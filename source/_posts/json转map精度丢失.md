---
title: json转map精度丢失
date: 2017-11-20 13:59:59
tags:
categories:
---

# 问题描述

支付返回结果（json 字符串）先转 map 再转对象时发生了精度丢失。字符串是`{"id":188838035603143129}`。转成 map，再转成对象时，对象对应的 long 类型字段变成`188838035603143130`。使用的是 gson.fromJson(String, Class)。gson 版本是
2.5。

# 问题原因

debug 发现，在 string 转成 map 时，值已经发生了变化。gson 默认是将字符串中的
number 转换成 double 类型。原 string 串`188838035603143129`，map 中的 value 是
1.88838035603143136E17，此时已经发生了精度丢失。

对比看 188838035603143129 1.88838035603143136E17

## long->double 精度丢失的原因

浮点型（float,double ）在内存中都是以科学计数法存储。以 double 双精度举例，内存中的结构是 1 11 52 符号位 指数位 尾数部分 2^52 = 4503599627370496，一共 16 位。

float 的精度是 6-7 位有效数字，6 位绝对是精确的。 double 的精度是 15-16 位有效数字，15 位是绝对精确的。 18 位的 id 字符串转成 double 时，存储的结果本该是
1.88838035603143129e17，但是由于精度问题，准确的只有 15 位也就是
888380356031431，后面的两位是截断后的结果，并不准确。对比实际调试时的结果：
1.88838035603143129e17--- 理想情况 1.88838035603143136e17--- 实际情况小数点后 15
位是准确的，再往后不再准确。

因此当 long 型的位数不大于 15 位时，先转 double，再转回 long，精度不会丢失。但是大于 15 位时，先转 double，再转回 long，精度会丢失。

## gson 转换 json 串中的 number 为 double 的原因

除了精度丢失的问题外，gson 将 json 串中的 long 转成 double 也是不合理的。

在 gson 的`ObjectTypeAdapter`类中可以看到：

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

在 case NUMBER 这一行可以看到，数值型的数据都被简单地处理为 Double 型的值。

# 解决办法

不经过 map 转换，直接将 json 串转换成对象。如果非要转成 map，那么可以使用其他的
json 库，比如 jackson 和 fastjson。

# 参考链接

<http://www.jianshu.com/p/c51041a791bd>
<http://www.zhyea.com/2016/11/13/gson-map-integer-double.html>
