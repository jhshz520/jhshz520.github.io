---
title: 蔡乐公式leetcode1185
date: 2019-10-12 15:20:10
tags: leetcode
---
1582年10月4日之后：w=y1+(y1/4)+(c/4)-2*c+(26*(m+1)/10)+d-1;

1582年10月4日以及之前：w=y1+y/4+c/4-2*c+13*(m+1)/5+d+2;
```
string weeks[7] = {"Sunday", "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday"};
        int month_days[12] = {31,28,31,30,31,30,31,31,30,31,30,31}; 
        // 1971-01-01 星期五
        int sum = 0;
        for(int i=1971; i<year; i++)
        {
            if( (i % 4 ==0 && i % 100 != 0) || (i % 400 == 0) )
                sum += 366;
            else
                sum += 365;
        }
        for(int i=0; i<month-1; i++)
        {
            sum += month_days[i];
            if(i == 1 && ((year % 4 ==0 && year % 100 != 0) || (year % 400 == 0)))
                sum += 1;
        }
        sum += day;
        return weeks[(sum + 4) % 7];
    }
```
