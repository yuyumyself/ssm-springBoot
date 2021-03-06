临时表：
	介绍：将查询结果作为另一个查询语句的来源
	例：
		select * from(select * from test)  a

表连接:
	介绍：
		表连接分内连接、外连接。
			内连接：是保证两个表中所有的行都要满足连接条件。
			外连接：某些不满条件的列也会显示出来。
				分左连接、右连接、全连接三种。

	inner join：内连接（join）
		介绍：
			简写join！
			内连接，返回左右表均有的值。
		例：
			写法1(inner join)：select * from client as c inner join financial_account as f on c.idcard = f.idcard
			写法2(join)：select * from client  c join financial_account  f on c.idcard = f.idcard
		注：
			内连接可以不加on，结果就是笛卡儿乘积。
			具体看goto： 笛卡尔积连接
	left outer join：左连接（left join）
		介绍：
		    简写left join。
		    左连接，左表数据全部显示，且左表有的右表没有的则显示为null。
		    附：
		        左表全展示，并不是指左连接结果数量一定与左表相等，而是左连接结果数量可能大等于左表。
		        因为左表一条记录在右表可能有2条对应的连接记录，那么左连接结果集中就有有两条记录。
		        注：
		            不只左连接，所有的连接都有这个逻辑。
		例：
		    ./左连接/
	right outer join：右连接（right join）
		介绍：
			简写right join。
			右连接，右表数据全部显示，且右表有的左表没有的则显示为null。
		例：
	full out join：全连接（full join）
		介绍：
			简写full join 。全连接，返回两表所有数据未匹配项用null代替。
				即：左连接结果与右连接结果的合并。
		附：
			MySQL中不支持 FULL OUTER JOIN，但我们可以用union模拟。
			mysql模拟实现全连接：
				SELECT * FROM t1
				LEFT JOIN t2 ON t1.id = t2.id
				UNION
				SELECT * FROM t1
				RIGHT JOIN t2 ON t1.id = t2.id
			上面的 SQL 语句还可以加上where条件从句，对记录进行筛选
	注：
	表连接优化事项
		1. 尽量要有关联条件，关联条件尽量有索引
		2. 尽量用有索引的where来优化至少一个表的查询
        原因：
            因为关联原理是：嵌套循环查询+where条件筛选
            具体goto：mysql如何执行关联查询
	附：
		cross join：（笛卡尔积连接、交叉连接）
			介绍：
				表 A（共有 n 条记录），表 B （共有 m 条记录），两表连接后，会产生一张包含 n x m 条记录的新表。
				附：
					笛卡儿乘积：第一个表中的每一行被联合到第二个表中的每一行
				附：
					通常用于表 A 和表 B 不存在关联字段
			例：
				select * from client  c cross join financial_account as f
			注：
				cross join 和 ,  和 INNER JOIN 区别：
					CROSS JOIN 与 INNER JOIN：
						在MySQL中，CROSS JOIN从语法上说与INNER JOIN等同(两者可以互相替换。)
						即CROSS JOIN 与 INNER JOIN是同一个东西。
                        附：
                            在标准SQL中，两者是不等同的。？
                            INNER JOIN与ON子句同时使用，CROSS JOIN以其它方式使用。
					逗号连接 与 inner join：
                        https://blog.csdn.net/huanghanqian/article/details/52847835
						INNER JOIN和，（逗号连接）功能相同(二者其实是同一个东西)，只是连接语法有些区别。
						附：
						论证：维基百科
                            SQL 定义了两种不同语法方式表示"连接"：
                                首先是"显式连接符号"：
                                    它显式地使用关键字 JOIN
                                其次是"隐式连接符号"：
                                    它使用所谓的"隐式连接符号"。即表之间用逗号分隔。
                            附：
                                SQL 89标准只支持内部连接与交叉连接，因此只有隐式连接这种表达方式；SQL 92标准增加了对外部连接的支持，这才有了 JOIN 表达式。
						逗号连接语法：
                            SELECT
                             语句的 
                            FROM
                             部分，并用逗号隔开。这样就构成了一个"交叉连接"，
                            WHERE
                             语句可能放置一些过滤谓词(过滤条件)。

							注:
								逗号连接，只能用where设置连接条件，无法使用on
					总结：
						CROSS JOIN 与 INNER JOIN 与 逗号连接 其实就是同一个东西，只是语法上略有区别
						例：
							以下语句效果都一样，且去掉连接条件结果都是笛卡儿积。
							逗号连接：
								select * from `user` , WRONG_IMG where user.id = WRONG_IMG.CREATOR_ID
							inner join：
	                            select * from `user` inner JOIN WRONG_IMG on user.id = WRONG_IMG.CREATOR_ID
	                            select * from `user` inner JOIN WRONG_IMG where user.id = WRONG_IMG.CREATOR_ID
                            cross JOIN：
	                            select * from `user` cross JOIN WRONG_IMG on user.id = WRONG_IMG.CREATOR_ID
	                            select * from `user` cross JOIN WRONG_IMG where user.id = WRONG_IMG.CREATOR_ID

		部分表连接案例：
			自连接:
				样例:
					select a.id,b.id,b.pid,b.name
					from test as a,test as b
					where a.id=b.pid
			多表连接：
				写法1：select * from student inner join teacher on student.id=teacher.sid inner join project on student.id=project.sid;
				写法2：select * from student,teacher,project where student.id=teacher.sid and student.id=project.sid;

	附：
		USING
			...

		NATURAL连接：
			...
		非等连接：
			介绍：使用等值以外的条件来匹配左、右两个表中的行 select A.c1,B.c2 from A join B on A.c3 != B.c3;

mysql如何执行关联查询：《高性能mysql》 p214
	介绍：
        当前MySQL(5.0)关联执行的策略很简单:MySQL对任何关联都执行嵌套循环关联操作，
        附：
            关联其实就2种：
                1. 笛卡尔积
                    原理：就是嵌套循环查询
                2. 根据关联字段进行关联
                    原理：嵌套循环查询+where条件筛选
            嵌套循环查询：
                即MySQL先在一个表中循环取出单条数据，然后再嵌套循环到下一个表中寻找匹配的行，依次下去，直到找到所有表中匹配的行为止。
                然后根据各个表匹配的行，返回查询中需要的各个列。
        附：！
            MySQL认为任何一个查询都是一次“关联”--并不仅仅是一个查 询需要到两个表匹配才叫关联，
            所以在MySQL中，每一个查询，每一个片段(包括子查询，甚至基于单表的SELECT)都可能是关联。
            即有些查询其实现就是嵌套循环查询。

	通过案例学习：
		SELECT tbl1.col1, tbl2.col2
		FROM tbl1 INNER J0IN tbl2 USING( col3)
		WHERE tbl1.col1 IN(5,6);
		解析：
			假设MySQL按照查询中的表顺序进行关联操作，我们则可以用下面的伪代码表示
			MySQL将如何完成这个查询:
				outer_iter = iterator over tbl1 where col1 IN(5,6)//1. 找出tab1所有符合where条件数据
				outer_row = outer_iter.next
				while outer_row //2. 遍历1结果
					inner_iter = iterator over tbl2 where col3 = outer_row.co13 //3. 关联字段通过where在tbl2中进行条件筛选
					inner_row = inner_iter.next
					while inner_row // 遍历输出3.结果
						output [outer_row.col1, inner_row.col2]
						inner_row = inner_iter.next
					end
					outer_row = outer_iter.next
				end
		附：
			通过泳道图展示MySQL如何完成关联查询:
				./关联查询泳道图.png
		附：
			是一定会先遍历tab1，在把tab1结果拿去循环tab2吗.
			    不是，mysql会根据内部的分析优化结果，选择先遍历那个表。
			    根据测试案例中(仅关联：)和(关联，并使用无索引的筛选条件：)可以得出该结论。
			关联中where的作用：
                能提高关联效率。
                mysql通常把有where筛选条件的作为循环外表。
                    根据测试案例中(仅关联：)和(关联，并使用无索引的筛选条件：)可以得出该结论。
    测试案例：
        EGOV_RECEIVAL：112887 索引：id主建、FLOW_PID索引
        FLOW_PROCESS_INSTANCE：122665 索引： id主建

        仅关联：
            SELECT * FROM EGOV_RECEIVAL r INNER JOIN FLOW_PROCESS_INSTANCE p ON r.FLOW_PID = p.ID
            //3s 查出96000条

            1	SIMPLE	r		ALL	testForJoin				107536	100.00	Using where
            1	SIMPLE	p		eq_ref	PRIMARY	PRIMARY	48	zjgy_real.r.FLOW_PID	1	100.00	Using where
            附：
            为什么第二个扫描行数为1：
                是因为嵌套循环查询原因吧，p表虽然1行，但被查询了107536次。？

        关联，并使用有索引的筛选条件：
            SELECT * FROM EGOV_RECEIVAL r INNER JOIN FLOW_PROCESS_INSTANCE p ON r.FLOW_PID = p.ID where p.ID = 'W-4lI-SwQwQ9PF0U';
            //0.034s 查出一条记录

            1	SIMPLE	p		const	PRIMARY	PRIMARY	48	const	1	100.00
            1	SIMPLE	r		ref	testForJoin	testForJoin	51	const	1	100.00

            分析：
                因为p有where条件筛选，所以先根据条件查询出p的数据，然后在把p的结果拿去r进行关联查询
        关联，并使用无索引的筛选条件：
            SELECT * FROM EGOV_RECEIVAL r INNER JOIN FLOW_PROCESS_INSTANCE p ON r.FLOW_PID = p.ID where p.DRAFT_USER_NO = '1300B00081217030';
            //0.4 查出4498条数据

            1	SIMPLE	p		ALL	PRIMARY				119184	10.00	Using where
            1	SIMPLE	r		ref	testForJoin	testForJoin	51	zjgy_real.p.ID	1	100.00	Using index condition

            分析：
                因为p有where条件筛选，所以先根据条件查询出p的数据，然后在把p的结果拿去r进行关联查询。
                p.DRAFT_USER_NO因为无索引，所以其就是全表遍历。

            附：
                where没有索引查询也比只查询快很多，很大可能时因为数据量大，大部分耗时在传输的过程了。
附：
连接算法：
	https://www.cnblogs.com/shangyu/p/6055181.html
	hash join是一种数据库在进行多表连接时的处理算法，对于多表连接还有两种比较常用的方式：sort merge-join 和 nested loop。 
	对于不同的查询方式，使用相同的join算法也会有不同的代价产生，对于具体使用哪一种连接方式是由优化器通过代价的衡量来决定的。
	nested loop join
		嵌套循环连接，是比较通用的连接方式，分为内外表，每扫描外表的一行数据都要在内表中查找与之相匹配的行。
			(即对嵌套的内表进行不断循环)
		附：
		    没有索引的复杂度是O(N*M)，这样的复杂度对于大数据集是非常劣势的，一般会通过索引来提升性能。
 　 sort merge-join
		merge join需要首先对两个表按照关联的字段进行排序，分别从两个表中取出一行数据进行匹配，如果合适放入结果集；不匹配将较小的那行丢掉继续匹配另一个表的下一行，依次处理直到将两表的数据取完。
		merge join的很大一部分开销花在排序上，也是同等条件下差于hash join的一个主要原因。
	hashjoin：
		https://www.cnblogs.com/shangyu/p/6055181.html
		原理：
			对于简单的两个表来讲，hash-join就算将两表中的小表（称S）作为hash表，然后去扫描另一个表(称M)的每一行数据，用得出来的行数据根据连接条件去映射建立的hash表，hash表是放在内存中的，这样可以很快的得到对应的S表与M表相匹配的行。
			hash-join只适用于等值连接，对于>, <, <=, >=这样的查询连接还是需要nested loop这种通用的连接算法来处理。
            附：
                对于结果集很大的情况，merge-join需要对其排序效率并不会很高，
                而nested loop join是一种嵌套循环的查询方式无疑更不适合大数据集的连接，
                而hash-join正是为处理这种棘手的查询方式而生，尤其是对于一个大表和一个小表的情况，基本上只需要将大小表扫描一遍就可以得出最终的结果集。
            附：
                如果连接key本来就是有序的或者需要排序，那么可能用merge-join的代价会比hash-join更小，此时merge-join会更有优势。
		附：
			mysql5.7不支持hashjoin，mysql8支持了hashjoin。
				https://dev.mysql.com/doc/refman/5.7/en/
				https://dev.mysql.com/doc/refman/8.0/en/hash-joins.html
			hashjoin其实还有很多需要考虑和实现的地方，比如数据倾斜严重如何处理、内存放不下怎木办，hash如何处理冲突等，这些并不是本文介绍的重点，不再详述，每个拿出来都可以再讲一篇了。