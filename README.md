# pdf2canvas

> 从服务器请求pdf文件转成canvas进行前端展示

## 解决无法显示盖章的问题

``` bash
# 将32251行代码注释掉就会显示电子签章。

  if (data.fieldType === 'Sig') {
    // _this2.setFlags(_util.AnnotationFlag.HIDDEN);
  }
```
