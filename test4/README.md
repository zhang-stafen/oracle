# 实验四: 对象管理
## 实验目的: 
了解Oracle表和视图的概念，学习使用SQL语句Create Table创建表，学习Select语句插入，修改，删除以及查询数据，学习使用SQL语句创建视图，学习部分存储过程和触发器的使用。
## 实验场景:
假设有一个生产某个产品的单位，单位接受网上订单进行产品的销售。通过实验模拟这个单位的部分信息：员工表，部门表，订单表，订单详单表。
## 实验内容:
### 删除表和序列
![delete.png](./delete.png)
### 创建表及表的修改:
1.部门表
![department.png](./department.png)
2.产品表
![products.png](./products.png)
3.员工表
![employees.png](./employees.png)
![employees2.png](./employees2.png)
4.订单表和订单详情表
![orders.png](./orders.png)
### 索引的创建:
![suoyin.png](./suoyin.png)
### 录入数据:
要求至少有1万个订单，每个订单至少有4个详单。至少有两个部门，每个部门至少有1个员工，其中只有一个人没有领导，一个领导至少有一个下属，并且它的下属是另一个人的领导（比如A领导B，B领导C）。
![insert1.png](./insert1.png)
![insert2.png](./insert2.png)
### 序列的应用:
插入ORDERS和ORDER_DETAILS 两个表的数据时，主键ORDERS.ORDER_ID, ORDER_DETAILS.ID的值必须通过序列SEQ_ORDER_ID和SEQ_ORDER_ID取得，不能手工输入一个数字。
![sequence.png](./sequence.png)
### 创建视图:
![view.png](./view.png)
### 触发器的应用:
维护ORDER_DETAILS的数据时（insert,delete,update）要同步更新ORDERS表订单应收货款ORDERS.Trade_Receivable的值。
![chufa.png](./chufa.png)
### 查询语句:
1.查询某个员工的信息。
select * from EMPLOYEES where EMPLOYEE_ID=1;
![查询1.png](./查询1.png)

2.递归查询某个员工及其所有下属，子下属员工。
WITH A (EMPLOYEE_ID,NAME,EMAIL,PHONE_NUMBER,HIRE_DATE,SALARY,MANAGER_ID,DEPARTMENT_ID) AS
  (SELECT EMPLOYEE_ID,NAME,EMAIL,PHONE_NUMBER,HIRE_DATE,SALARY,MANAGER_ID,DEPARTMENT_ID
    FROM employees WHERE employee_ID = 11
    UNION ALL
  SELECT B.EMPLOYEE_ID,B.NAME,B.EMAIL,B.PHONE_NUMBER,B.HIRE_DATE,B.SALARY,B.MANAGER_ID,B.DEPARTMENT_ID
    FROM A, employees B WHERE A.EMPLOYEE_ID = B.MANAGER_ID)
SELECT * FROM A;
![查询2.png](./查询2.png)

3.查询表的统计信息。
select table_name,tablespace_name,num_rows from user_tables where table_name='ORDERS';
select table_name,tablespace_name,num_rows from user_tables where table_name='ORDER_DETAILS';
![查询3.png](./查询3.png)


4.查询订单详表，要求显示订单的客户名称和客户电话，产品类型用汉字描述。
select orders.customer_name,orders.customer_tel,product_type from (
select products.product_type,order_id from order_details left join products on order_details.product_name=products.product_name)a
left join orders on orders.order_id=a.order_id
![查询4.png](./查询4.png)

5.查询出所有空订单，即没有订单详单的订单。
select orders.order_id from orders where order_id not in(
select order_id from order_details)
![查询5.png](./查询5.png)

6.查询部门表，统计每个部门的销售总金额。
select department_name,count(price) from departments left join(
select employees.department_id,order_id,price from employees left join(
select orders.employee_id, orders.order_id,price from orders left join( 
select order_id, product_num*product_price as price from order_details)a on a.order_id=orders.order_id)b on b.employee_id=employees.employee_id)c on c.department_id=departments.department_id
group by departments.department_name
![查询7.png](./查询7.png)
## 实验总结
通过实验我学会了使用SQL语句Create Table创建表，还有Select语句插入，修改，删除以及查询数据等相关语法，最后还学习了如何使用SQL语句创建视图，学习部分存储过程和触发器的使用。SQL语句的增删查改是之前学过的，通过练习就可以很快明白其方法原理，但是视图的创建和触发器的使用我是第一次接触，在实验过程中一直遇到问题，不知道该怎么解决，之后通过询问老师和同学以及网上搜索明白了相关原理语法，最终解决了问题，这次实验让我学习了更多新知识。