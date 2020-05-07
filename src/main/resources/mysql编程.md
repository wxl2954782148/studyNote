# mysql存储过程

存储过程语法

```mysql
delimiter $
create procedure sp_name ([参数,参数..])
begin
执行体
end $
delimiter;
```

执行存储过程

```mysql
call sp_name();
```

删除存储过程

```mysql
mysql存储过程没有修改功能，只能先删除再创建
drop procedure sp_name;
```

局部变量：在begin end块中有效

```mysql
语法：
declare var_name type [default var_value];
eg: declare nickname varchar(32);
	declare nickname varchar(32) default 'AA';
赋值：
set nickname = 'ZS';
```

用户变量：当前会话有效

```mysql
语法：
	@var_name;
eg
 set nickname = 'ZS';
```

Into赋值

```mysql
delimiter $
create procedure sp_into()
begin
	select phone_title into @title from _j_d_item where item_id = 50184;
end $
delimiter;

call sp_into();
select @title;
```

入参出参

```mysql
语法
in | out | inout param_name type
```

in:传入参数

```mysql
delimiter $
create procedure sp_in(in aSpu varchar(32))
begin
	select phone_title from _j_d_item where spu = aSpu;
end $
delimiter;
call sp_in('100009082500');
```

out:输入参数

```mysql
delimiter $
create procedure sp_out(in itemId int, out title varchar(255))
begin
	select phone_title into title from _j_d_item where item_id = itemId;
end $
delimiter;

call sp_out(50181, @title);
select @title;
```

inout:入参出参

```mysql
delimiter $
create procedure sp_inout(inout num int)
begin
	set num = 3 + num;
end $
delimiter;

set @num = 1;
call sp_inout(@num);
select @num;
```

流程控制

```mysql
if语句
if a > 0 
  then ...
elseif a = 0
  then ...
else
  ...
end if;

```

```mysql
case语句 :case可以用在存储过程和查询语句
case search_condition
  when ... then ...
  when ... then ...
  else ...
end case;  

case
	when search_condition then ...
	when search_condition then ...
	else ...
end case; 
```

循环

```mysql
loop循环(for)：
delimiter $
create procedure sp_loop()
begin
	declare i int default 1;
	declare sum int default 0;

	loop_Name: loop 
		if i > 10
			then leave loop_Name;		-- 循环终止条件 leave:退出循环(break)						end if;						    -- iterate:跳出循环（continue）

		set sum = sum + i;
		set i = i + 1;
	end loop loop_Name;
	
	select sum;
end $ 
```

```mysql
repeat循环(do...while)：
delimiter $
create procedure sp_repeat2(in count int)
begin
	declare i, res int default 0;
	repeat_name: repeat 
		set i = i + 1;
		set res = res + i;
		until i = count
	end repeat repeat_name;
	select res;
end $
delimiter;
call sp_repeat2(100);
```

```mysql
while循环:
delimiter $
create procedure sp_while()
begin
	declare i, res int default 0;

	while i < 10 do 
		set res = res + i;
		set i = i + 1;
	end while;
  select res;
end $
delimiter;
call sp_while();
```

#### 游标

```mysql
--声明语法
declare cursor_name cursor for select_statement  -- select_statement:需要游标遍历的结果集
--打开游标
open cursot_name
--取值
fetch cursor_name into var_name[, var_name]...
--关闭游标
close cursor_name
```

```mysql
drop procedure sp_cursor1;
delimiter $
create procedure sp_cursor1()
begin 
	declare sku varchar(200);
	declare res varchar(8000) default ',';
	declare lp_flag boolean default true;		

	declare cur_test cursor for 
		select sku from _j_d_item i, _j_d_detail d where i.item_id = d.item_id and i.item_id = 50181;				-- 指定游标的结果集
	
	declare continue handler for not found set lp_flag = false;	-- 当游标为not found时停止
	open cur_test;

	posLoop: loop 
		fetch cur_test into sku;		-- 逐行取值

		if lp_flag then 
			set res = concat(res, sku);
		else
			leave posLoop;
		end if;
		
	end loop posLoop;
	select sku;
	close cur_test;
end $
call sp_cursor1()
```





















