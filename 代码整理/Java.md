## List调用toString()方法后，去除两头的中括号

```Java
import org.apache.commons.lang.StringUtils;  
  
public class Test {  
    public static void main(String[] args) {  
          
        List<String> list = new ArrayList<String>();  
        list.add("1");  
        list.add("2");  
        list.add("3");  
          
        System.out.println(list.toString());  
        System.out.println(StringUtils.strip(list.toString(),"[]"));  
    }  
  
}  

打印内容：  
[1, 2, 3]  
1, 2, 3  
```

