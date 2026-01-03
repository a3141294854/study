# Go 反射函数速查

## reflect.TypeOf 相关

| 函数                  | 返回类型                    | 作用           | 示例                           |
| --------------------- | --------------------------- | -------------- | ------------------------------ |
| `reflect.TypeOf(x)`   | `reflect.Type`              | 获取类型信息   | `t := reflect.TypeOf(42)`      |
| `t.Name()`            | `string`                    | 类型名称       | `t.Name()` → `"int"`           |
| `t.Kind()`            | `reflect.Kind`              | 类型种类       | `t.Kind()` → `reflect.Int`     |
| `t.PkgPath()`         | `string`                    | 包路径         | `t.PkgPath()` → `"main"`       |
| `t.NumField()`        | `int`                       | 结构体字段数量 | `t.NumField()` → `2`           |
| `t.Field(i)`          | `reflect.StructField`       | 第 i 个字段    | `t.Field(0).Name` → `"Name"`   |
| `t.FieldByName(name)` | `reflect.StructField, bool` | 按名查找字段   | `t.FieldByName("Name")`        |
| `t.Elem()`            | `reflect.Type`              | 元素类型       | `t.Elem()` → `int` 的类型      |
| `t.Key()`             | `reflect.Type`              | 映射键类型     | `t.Key()` → `string` 的类型    |
| `reflect.SliceOf(t)`  | `reflect.Type`              | 创建切片类型   | `reflect.SliceOf(t)` → `[]int` |
| `reflect.PtrTo(t)`    | `reflect.Type`              | 创建指针类型   | `reflect.PtrTo(t)` → `*int`    |

## reflect.ValueOf 相关

| 函数                  | 返回类型        | 作用             | 示例                                |
| --------------------- | --------------- | ---------------- | ----------------------------------- |
| `reflect.ValueOf(x)`  | `reflect.Value` | 获取值           | `v := reflect.ValueOf(42)`          |
| `v.Kind()`            | `reflect.Kind`  | 值的种类         | `v.Kind()` → `reflect.Int`          |
| `v.Type()`            | `reflect.Type`  | 值的类型         | `v.Type()` → `int` 的类型           |
| `v.Int()`             | `int64`         | 整数值           | `v.Int()` → `42`                    |
| `v.Uint()`            | `uint64`        | 无符号整数值     | `v.Uint()` → `42`                   |
| `v.Float()`           | `float64`       | 浮点数值         | `v.Float()` → `3.14`                |
| `v.String()`          | `string`        | 字符串值         | `v.String()` → `"hello"`            |
| `v.Bool()`            | `bool`          | 布尔值           | `v.Bool()` → `true`                 |
| `v.Interface()`       | `interface{}`   | 转为 interface{} | `v.Interface()` → `42`              |
| `v.NumField()`        | `int`           | 字段数量         | `v.NumField()` → `2`                |
| `v.Field(i)`          | `reflect.Value` | 第 i 个字段值    | `v.Field(0).String()` → `"Alice"`   |
| `v.FieldByName(name)` | `reflect.Value` | 按名查找字段值   | `v.FieldByName("Age").Int()` → `25` |
| `v.Elem()`            | `reflect.Value` | 指针指向的值     | `v.Elem().Int()` → `42`             |
| `v.IsZero()`          | `bool`          | 是否为零值       | `v.IsZero()` → `true`               |
| `v.IsValid()`         | `bool`          | 是否有效         | `v.IsValid()` → `true`              |
| `v.CanSet()`          | `bool`          | 是否可修改       | `v.CanSet()` → `true`               |
| `v.CanAddr()`         | `bool`          | 是否可寻址       | `v.CanAddr()` → `true`              |
| `v.SetInt(x)`         | -               | 设置整数值       | `v.SetInt(42)`                      |
| `v.SetFloat(x)`       | -               | 设置浮点数值     | `v.SetFloat(3.14)`                  |
| `v.SetString(x)`      | -               | 设置字符串值     | `v.SetString("hello")`              |
| `v.SetBool(x)`        | -               | 设置布尔值       | `v.SetBool(true)`                   |

## reflect.New 相关

| 函数             | 返回类型        | 作用             | 示例                  |
| ---------------- | --------------- | ---------------- | --------------------- |
| `reflect.New(t)` | `reflect.Value` | 创建新值（指针） | `v := reflect.New(t)` |

## reflect.StructField 相关

| 函数         | 返回类型            | 作用     | 示例                               |
| ------------ | ------------------- | -------- | ---------------------------------- |
| `field.Name` | `string`            | 字段名   | `field.Name` → `"Name"`            |
| `field.Type` | `reflect.Type`      | 字段类型 | `field.Type` → `string` 的类型     |
| `field.Tag`  | `reflect.StructTag` | 字段标签 | `field.Tag.Get("json")` → `"name"` |

## 常见场景速查

### 场景 1：获取结构体字段值

```go
p := Person{Name: "Alice", Age: 25}
v := reflect.ValueOf(p)
name := v.FieldByName("Name").String()  // "Alice"
age := v.FieldByName("Age").Int()        // 25
```

### 场景2：创建切片

```go
// 1. 获取元素类型
elemType := reflect.TypeOf(list.Model).Elem()  // models.LuggageStorage 的 reflect.Type

// 2. 创建切片类型
sliceType := reflect.SliceOf(elemType)         // []models.LuggageStorage 的 reflect.Type

// 3. 创建切片值（指针）并解引用
sliceValue := reflect.New(sliceType).Elem()     // []models.LuggageStorage 的 reflect.Value

// 4. 转换为 interface{}
res := sliceValue.Interface()                  // []models.LuggageStorage


```

