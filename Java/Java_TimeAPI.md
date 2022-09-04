# System中的currentTimeMillis()
```
long time = System.currentTimeMillis(); // 返回当前时间与1970年1月1日0时0分0秒之间以毫秒为单位的时间差，称为时间戳
```

# java.util.Date 和 java.sql.Date
```
构造器：
Date():创建一个对应当前时间的Date对象
Date(long time):创建指定时间戳的Date对象
方法：
toString()显示当前的年月日时分秒
getTime()获取当前Date对象对应的毫秒数
两个Date对象的转换：
Date date = new Date();
java.sql.Date date1 = new java.sql.Date(date.getTime());
```

# SimpleDateFormat
```
......
```

# Calendar
```
Calendar calendar = Calendar.getInstance();
int days = calendar.get(Calendar.DAY_OF_MONTH);
```

# LocalDate，LocalTime，LocalDateTime，DateTimeFormatter
```
......
```