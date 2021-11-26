#### 1.1 Python 操作 Excel 报表自动化指南

##### 1.11 xlrd读取表格

- 介绍

  - python操作excel主要用到xlrd和xlwt这两个库，即xlrd是读excel，xlwt是写excel的库。

  - 在UI自动化或者接口自动化中数据维护是一个核心，所以此模块非常实用。



- 使用

  ![img](https://github.com/Dosrui78/Blog-Images/blob/master/1.11.jpg?raw=true)

  ```
  import xlrd
  wb = xlrd.open_workbook(os.path.abspath(__file__) + '/../info.xlsx')
  sheet = wb.sheets()[0] # 通过索引顺序获取
  sheet1 = wb.sheet_by_name('Sheet1')  # 通过名称获取
  sheet2 = wb.sheet_by_index(0) # 通过索引顺序获取
  print(sheet == sheet1 == sheet2)  # True
  print(sheet.sheet_names()) # 返回book中所有工作表的名字
  ```

  ```
  rows = sheet.nrows  # 获取行数
  cols = sheet.ncols  # 获取列数
  print(rows, cols)
  for i in range(rows):
      print(sheet.row(i)) # 获取每一行，以列表形式输出(output：1.11-1)
      print(sheet.row(i)[0].value) # 获取每一行第一列的数据(output: 1.11-2),不加value则返回的是对象
  ```

  ![1.11-1.jpg](https://github.com/Dosrui78/Blog-Images/blob/master/1.11-1.jpg?raw=true "1.11-1")

![1.11-2.png](https://github.com/Dosrui78/Blog-Images/blob/master/1.11-2.png?raw=true "1.11-2")

```
sheet.col(colx, start_rowx=0, end_rowx=None)
    # 返回由该列中所有的单元格对象组成的列表

sheet.col_slice(colx, start_rowx=0, end_rowx=None)
    # 返回由该列中所有的单元格对象组成的列表

sheet.col_types(colx, start_rowx=0, end_rowx=None)
    # 返回由该列中所有单元格的数据类型组成的列表

sheet.col_values(colx, start_rowx=0, end_rowx=None)
    # 返回由该列中所有单元格的数据组成的列表
    
print(sheet.cell(2,0))  # 获取第三行，第一列的数据output:  text:'武汉大学'
print(sheet.cell_type(2,0))  # 获取第三行，第一列的数据类型output:  text:'武汉大学'
print(sheet.cell_value(2, 0)) # 获取第三行，第一列的数据output: '武汉大学'
print(sheet.cell(rowx=2, colx=0))  # 获取第三行，第一列的数据output:  text:'武汉大学'
```

----



##### 1.12 xlwt写入表格

>  xlwt可以用于写入新的Excel表格或者在原表格基础上进行修改，速度也很快，推荐使用！

- 常用操作

```
import xlwt
def fun3_2_2():
    # 创建新的workbook（其实就是创建新的excel）
    workbook = xlwt.Workbook(encoding= 'ascii')

    # 创建新的sheet表
    worksheet = workbook.add_sheet("My new Sheet")

    # 往表格写入内容
    worksheet.write(0,0, "内容1")
    worksheet.write(2,1, "内容2")

    # 保存
    workbook.save("新创建的表格.xls")

fun3_2_2() #结果如下图1.12
```

![1.12.png](https://github.com/Dosrui78/Blog-Images/blob/master/1.12.png?raw=true "1.12")

