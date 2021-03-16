### 201810112302 软工一班邓永霜
## 实验1：SQL语句的执行计划分析与优化指导
### 实验目的 
分析SQL执行计划，执行SQL语句的优化指导。理解分析SQL语句的执行计划的重要作用。
### 实验步骤
#### 查询一代码
### 
    SELECT d.department_name,count(e.job_id)as "部门总人数",
	avg(e.salary)as "平均工资"
	from hr.departments d,hr.employees e
	where d.department_id = e.department_id
	and d.department_name in ('IT','Sales')
	GROUP BY d.department_name;

#### oracle优化指导
查找结果：创建一个或多个索引可以改进此语句的执行计划。
建议：可以创建推荐的索引。
原理：使用典型的 SQL 工作量运行优化指导可以获得全面的索引建议案, 以及计算时的开销其他空间消耗。
#### 优化设计截图
![Image text](1.png)
#### 查询二代码
#### 
    SELECT d.department_name,count(e.job_id)as "部门总人数",
    avg(e.salary)as "平均工资" 
    FROM hr.departments d,hr.employees e
    WHERE d.department_id = e.department_id
    GROUP BY d.department_name
    HAVING d.department_name in ('IT','Sales');
#### 优化设计截图
![Image text](2.png)
#### oracle优化指导
查找结果：无
建议：无，已为最佳优化设置
#### 自己设计的代码
#### 
    SELECT d.department_name,count(e.job_id)as "部门总人数",
	avg(e.salary)as "平均工资"
	from hr.departments d
    INNER JOIN
    hr.employees e
	on d.department_id = e.department_id
	and d.department_name='IT'
    OR  d.department_name='Sales'
	GROUP BY d.department_name;
#### oracle优化指导
查找结果：在执行计划中有几处发现开销很大的笛卡尔积操作。
建议：可以添加引用它的联接条件。
原理：笛卡尔积操作的开销很大且会产生大量数据。

#### 优化设计截图
![Image text](own.png)

### 实验总结
 通过这次实验，我明白了如何分析SQL执行计划，执行SQL语句的优化指导。并能够通过相应的要求自己慢慢修改代码，在一点点进步，希望再接再厉。