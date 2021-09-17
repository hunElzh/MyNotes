### EasyExcel的基本使用

创建三个java文件

1. ExcelUtil：其中包含了俩主要方法
   1. readRow(String filePath, ReadSheet sheet)：读取对应excel文件的sheet页
   2. dataReflet(String classPath, List<Object> objectList)：将读取到的 Object 对象转换成 json
2. ExcelListener：监听器，当调用read()方法时会被调用，用于读取excel数据
3. StudentInfo：与excel对接的实体类，将json格式对象与之结合，随后转换为特殊字符串（感觉没啥卵用

~~~ java
import com.alibaba.excel.EasyExcelFactory;
import com.alibaba.excel.read.builder.ExcelReaderBuilder;
import com.alibaba.excel.read.metadata.ReadSheet;
import com.alibaba.excel.write.metadata.WriteSheet;
import com.alibaba.fastjson.JSONArray;
import com.alibaba.fastjson.JSONObject;
import org.springframework.util.StringUtils;

import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.IOException;
import java.io.InputStream;
import java.lang.reflect.Field;
import java.util.LinkedHashMap;
import java.util.List;
import java.util.Map;

public class ExcelUtil {
    private static ReadSheet initReadSheet;
    private static WriteSheet initWriteSheet;

    static {
        initReadSheet = new ReadSheet(1, "sheet");
        initWriteSheet = new WriteSheet();
        initWriteSheet.setSheetName("sheet");
        initWriteSheet.setSheetNo(1);
    }

    /**
     * 1、读取excel
     */
    public static List<Object> readRow(String filePath, ReadSheet sheet) {
        if (!StringUtils.hasText(filePath)) {
            return null;
        }
        if (sheet != null) {
            sheet = sheet;
        } else {
            sheet = initReadSheet;
        }
        InputStream fileStream = null;
        ExcelListener excelListener = new ExcelListener();
        ExcelReaderBuilder builder = null;

        try {
            fileStream = new FileInputStream(filePath);
            //调用read()方法时，触发Listener，将文件流传入后读取，最后返回ExcelReaderBuilder对象
            builder = EasyExcelFactory.read(fileStream, excelListener);
            builder.doReadAll();
//            builder.sheet().doRead();     //读取第一个sheet页
            return excelListener.getDatas();
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } finally {
            try {
                if (fileStream != null) {
                    fileStream.close();
                }
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
        return null;
    }

    /**
     * 2、转换成对象
     */
    private static JSONArray dataReflet(String classPath, List<Object> objectList) {
        JSONArray jsonArr = new JSONArray();
        try {
            Class<?> clazz = Class.forName(classPath);
            Field[] importField = clazz.getDeclaredFields();
            for (Object obj : objectList) {
                Map<Integer, String> map = (LinkedHashMap) obj;
                String[] values = map.values().toArray(new String[0]);
                JSONObject jsonObj = new JSONObject();
                for (int i = 0; i <= values.length; i++) {
                    Field field = importField[i];
                    // 字段为 serialVersionUID时不读取
                    if ("serialVersionUID".equals(field.getName())) {
                        continue;
                    } else {
                        jsonObj.put(field.getName(), values[i - 1]);
                    }
                }
                jsonArr.add(jsonObj);
            }
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        }
        return jsonArr;
    }

    public static void main(String[] args) {
        String filePath = "C:\\Users\\User\\Desktop\\1.xls";
        String classPath = "com.lzh.test.util.excel.StudentInfo";

        //读取excel文件
        List<Object> objectList = ExcelUtil.readRow(filePath, null);
        System.err.println(objectList.toString());
        //将读取到的文件数据转换成为json数组
        JSONArray jsonArr = dataReflet(classPath, objectList);
        System.err.println(jsonArr);
        //将json格式的数据转化为一个不知名的格式
        List<StudentInfo> dtoList = JSONArray.parseArray(jsonArr.toJSONString(), StudentInfo.class);
        System.err.println(dtoList.toString());
    }
}
~~~



~~~ java
package com.lzh.test.util.excel;

import com.alibaba.excel.context.AnalysisContext;
import com.alibaba.excel.event.AnalysisEventListener;

import java.util.ArrayList;
import java.util.List;
import java.util.Map;

public class ExcelListener extends AnalysisEventListener<Object> {

    private int headSize;

    private List<Object> datas = new ArrayList<>();

    /**
     * 逐行解析数据
     * object：当前行的数据
     */
    @Override
    public void invoke(Object object, AnalysisContext context) {
        if (object != null) {
            datas.add(object);
        }
    }

    /**
     * 设置表头
     * @param headMap 存储excel中
     * @param context
     */
    @Override
    public void invokeHeadMap(Map<Integer, String> headMap, AnalysisContext context) {
        super.invokeHeadMap(headMap, context);
        headSize = headMap.size();
    }

    public int getHeadSize() {
        return headSize;
    }

    public void setHeadSize(int headSize) {
        this.headSize = headSize;
    }

    @Override
    public void doAfterAllAnalysed(AnalysisContext analysisContext) {
        //销毁不用的资源
    }

    public List<Object> getDatas() {
        return datas;
    }
}
~~~



~~~ java
import lombok.Data;

import java.io.Serializable;

@Data
public class StudentInfo implements Serializable {
    private static final long serialVersionUID = 7566235228027597205L;

    private String studnetName;

    private String gender;

    private String classes;

    private double score;
}
~~~



