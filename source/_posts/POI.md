---
title: POI导出bug总结
date: 2018-01-23 12:01:17
tags:
---
# <center>项目中遇到的bug总结</center>
---
## 1.基于反射的poi导出
    
##### 获得order中的所有属性
    Field[] fields = orderClass.getDeclaredFields();
    List<String> fieldsName = Arrays.stream(fields).map(Field::getName).collect(toList());
#####  创建行和列，将传来的类型和当前属性进行对比,反射得到属性
    String firstLetter = titleType[n].substring(0, 1).toUpperCase();
    String methodName = "get" + firstLetter + titleType[n].substring(1);
    Method method = orderClass.getDeclaredMethod(methodName);
#####  如果是date格式的需要特殊转换
    CellStyle cellStyle = sxssfWorkbook.createCellStyle();
    DataFormat format = sxssfWorkbook.createDataFormat();
    cellStyle.setDataFormat(format.getFormat("yyyy-mm-dd hh时MM分ss秒"));
    cell.setCellStyle(cellStyle);
    sheet.autoSizeColumn(n, true);
#####  若传来的为对象里的子对象,根据a得到子对象,根据b得到子对象的属性（归队格式为a.b，后面根据.进行切割）
    Method method = orderClass.getDeclaredMethod(methodName);
    Order currentOrder = list.get(i);
    Field field = orderClass.getDeclaredField(attr1);
    Class<?> type = field.getType();
    Type genericType = field.getGenericType();
##### 此处为List集合，取集合的泛型，根据属性b反射得到属性
    Class genericClazz = (Class)pt.getActualTypeArguments()[0];
    Method declaredMethod = genericClazz.getDeclaredMethod(methodName2);
    Object invoke1 = declaredMethod.invoke(invoke.get(0));
##### 导出
    File file = new File(excelName);
    OutputStream outputStream = new FileOutputStream(file);
    sxssfWorkbook.write(outputStream);
##### oss上传，由于上传的方法在另外一个微服务中,所以要跨服务调用
    FileSystemResource fileSystemResource = new FileSystemResource(excelName);
    String url = CList.ServiceUrl.DOCUMENT_SERVICE_URL + "document/uploadAndSave";
    HttpHeaders headers = new HttpHeaders();
    MediaType type = MediaType.parseMediaType("multipart/form-data");
    headers.setContentType(type);
    headers.add("X-CURRENT-USER-ID", String.valueOf(currentUserId));
    MultiValueMap<String, Object> form = new LinkedMultiValueMap<String, Object>();
    form.add("file", fileSystemResource);
    form.add("scheduleId",condition.get("scheduleId"));
    HttpEntity<MultiValueMap<String, Object>> formEntity = new HttpEntity<MultiValueMap<String, Object>>(form, headers);
    restTemplate.postForEntity(url, formEntity, Result.class);

## 遇到的问题
    1.生产端接收的类为multipart，跨服务调用的时候不能使用multipart的实现类进行传递，否则会出
    找不到multipart类型的异常（使用FileSystemResource）。
    2.项目中springboot启动类中设置了文件转换类型messageConvert为fastJson，导致传递文件的时候文件丢失。需要加上一个文件类型的转换器
    @Bean
    public RestTemplate restTemplate(
        HttpMessageConverters fastJsonHttpMessageConverters) {
        HttpMessageConverter converter = new FormHttpMessageConverter();
        List<HttpMessageConverter<?>> converterList = new ArrayList<>();
        converterList.add(converter);
        converterList.addAll(fastJsonHttpMessageConverters.getConverters());
        RestTemplate restTemplate = new RestTemplate(converterList);

        return restTemplate;
    }
    3.反射只能得到本类的属性。继承的baseEntity的属性得不到，这里的做法是直接把父类的
    属性重写了一遍（巨low，反射有方法可以获得父类的属性）。

