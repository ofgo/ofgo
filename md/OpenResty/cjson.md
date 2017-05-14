# CJSON

JSON 编码和解码。

## 介绍

OpenResty 自带了 [CJSON](https://github.com/openresty/lua-cjson/) 库，不需要另外安装。它 fork 自 [mpx/lua-cjson](https://github.com/mpx/lua-cjson) 。

## API

除了标准的编码（ `CJSON.encode()` ）和解码（ `CJSON.decode()` ）接口，OpenResty 自带的 CJSON 版本还新增了以下接口：

* `CJSON.encode_empty_table_as_object(true|false|"on"|"off")` : 该函数设置了 Lua 空表的编码行为；默认的，空表会被编码成 `{}`，当设置为 `false` 或 `"off"` 时，会被编码成 `[]` 。

* `CJSON.empty_array` : 此对象会被编码成 `[]` 。

* `CJSON.empty_array_mt` : 以 `setmetatable` 的方式把一个表中的空对象标记为 `[]` 。

* `CJSON.encode_number_precision(precision)` : 设置对数字的编码精度，最高可达小数点后16位。

CJSON其他的设置请参考 [CJSON Munal](https://www.kyne.com.au/~mark/software/lua-cjson-manual.html#cjson_encode) ，一般情况都用不上这些设置。
